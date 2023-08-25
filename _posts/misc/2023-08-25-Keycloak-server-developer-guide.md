---
title: "Keycloak server developer guide"
last_modified_at: 2023-08-25T07:16:02-05:00
categories:
  - Blog
tags:
  - Keycloak
  - SPI
  - Server developer guide
---
> Keycloak은 ID와 접근관리 기능을 제공하는 오픈소스이다. OAuth2, SAML2 표준 규격을 지원하고 외부 IDP와 연계해서 SSO를 제공이 가능하다.    
> 또한 Open ID Connect 표준규격으로 자체 사용자 ID를 관리할 수 있으며, 이미 존재하는 사용자 정보 Storage와 연계할 수도 있다.   
> Windows, Linux 환경과 Standalone, Docker, K8S 환경을 지원하기 때문에 활용 범위가 넓다.  
> 어지간 하면 별도의 코딩 없이 (제공되는) 관리자 화면만으로도 기업 또는 어플리케이션에서의 사용자 인증 및 권한관리 기능을 수행할 수 있지만,   
> 기능의 확장 또는 기존 시스템과의 연계 또는 활용 등이 필요할 경우에는 Keycloak 기능 확장을 위해서 추가 개발이 필요할 수도 있다.
> [공식 개발자 사이트](https://www.keycloak.org/docs/latest/server_development/)에 가이드가 자세하게 나와 있으나,   
> 모든 것이 그렇듯 (환경차이 등으로) 문서대로 되지 않는 부분들이 꽤 있다.   
> 이번 블로그에서는 [공식 개발자 사이트](https://www.keycloak.org/docs/latest/server_development/)에 있는 내용들을 (실제 예제와 함께)   
> 좀 더 쉽게 설명하고자 한다 (한번에 모두 할 수는 없고, 지속적/점증적으로 조금씩 내용을 추가할 계획이다)

<br/>
[서버개발자가이드](https://www.keycloak.org/docs/latest/server_development/)를 보면 아래의 그림과 같은 내용들이 있다.  
<br/>
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d847d91a-eb3a-42ee-a1ee-9c4b3cb7e473)   

대충 살펴보면 Keycloak에서 제공하는 관리자용 REST API를 사용하는 방법, Custom 테마적용 또는 테마선택하는 방법, Custom User Storage 추가하는 방법 등,   
개발자가 Keycloak과의 연계 또는 Keycloak Plugin을 만드는 방법 등을 설명하고 있다.  

한가지 유의할 점은 Keycloak의 플러그인 개발언어는 "자바"만 지원한다는 점이다. KeyCloak 자체가 자바로 만들어진 웹 어플리케이션이니, 어찌보면 당연하다.  

이번에는 그 중에서 가장 유용하고 필요할 것으로 판단되는 (기존 DBMS에 저장된) 사용자 정보를 활용하는 방법에 대해서 설명한다.   
<br/>
#### User Storage SPI
User Storage SPI는 사용자정보를 담고 있는 (이미 존재하는) Storage (DBMS 또는 File)를 재사용해서 (Keycloak에서) 사용자 인증 및 인가를 구성하기 위함이다.   
쉽게 말해서 Keycloak이 인식할 수 있는 형태의 플러그인 (jar 파일)을 "Keycloak_HOME/providers" 라는 디렉토리에 넣어주고 Keycloak을 구동하게 되면,  
Keycloak이 읽어 들여서 관리자가 ""User federation" 메뉴에서" 선택할 수 있도록 보여준다.  

MariaDB의 사용자 테이블에 사용자아이디, 사용자이름, 이메일주소, 비밀번호, 전화번호를 저장하고 있는 경우를 예로 들어서 Custom User Federation 플러그인을 만들어 보겠다.  
참고로 공식 개발자 가이드 사이트에는 두 개의 예제 코드를 github를 통해서 제공하고 있으니 참고한다.
- [user-storage-jpa](https://github.com/keycloak/keycloak-quickstarts/tree/latest/extension/user-storage-jpa)
- [user-storage-simple](https://github.com/keycloak/keycloak-quickstarts/tree/latest/extension/user-storage-simple) 

1. 이클립스 프로젝트 구조
   - Custom User Fedration을 위한 프로젝트는 Maven을 기준으로 하였다 (공식 사이트의 예제도 Maven임)
   - 아래는 이클립스 프로젝트 구조를 보여준다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/cedae6c2-120f-4844-9f92-6a732cb188cf)  

2. 주요 설정파일 설명
   - CustomUserAdapter, CustomUserEntity, CustomUserStorageProvider, CustomUserStorageProviderFactory에 대해서는 아래에서 자세히 기술한다.
   - META-INF/service/ 디렉토리 아래에 org.keycloak.storage.UserStorageProviderFactory라는 파일을 생성해 줘야 한다. 파일의 내용은 아래 코드와 같은 내용이 포함되면 된다. 만약 Factory가 여러개일 필요가 있다면 여러 개를 필요한 만큼 기입하면 된다.   

```
# org.keycloak.storage.UserStorageProviderFactory 파일의 내용
# {package path}.{provider factory file name}
com.sds.auth.keycloak.spi.CustomUserStorageProviderFactory
# 만약 필요하면 더 기입하면 된다.
```  

   - conf/quarkus.properties 파일은 데이터 소스정보를 기입하기 위한 파일이다. 이 파일은 keycloak runtime 시에는 "$Keycloak_HOME/conf" 디렉토리에 복사되어야 한다. 파일의 내용은 다음과 같다.  
```
# quarkus.properties 파일 내용
# user-store는 persitence.xml에서 정의된 persitence-unit의 이름과 동일해야 한다
quarkus.datasource.user-store.db-kind=mariadb
quarkus.datasource.user-store.username=DB계정
quarkus.datasource.user-store.password=DB비밀번호
quarkus.datasource.user-store.jdbc.url=jdbc:mariadb://localhost:3306/<데이터베이스이름>
```
   - META-INF/persistence.xml 파일은 사용자 정보에 대한 Entity 매핑정보과 Persistence 정보를 기록하기 위함이다. 파일의 내용은 다음과 같다.   
```
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="https://jakarta.ee/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="https://jakarta.ee/xml/ns/persistence https://jakarta.ee/xml/ns/persistence/persistence_3_0.xsd"
             version="3.0">
    <persistence-unit name="user-store" transaction-type="JTA">
        <class>com.sds.auth.keycloak.spi.CustomUserEntity</class>
        <properties>
            <!--<property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect" />-->
            <!--<property name="hibernate.connection.driver_class" value="org.h2.Driver" />-->
            <property name="hibernate.dialect" value="org.hibernate.dialect.MariaDBDialect" />
            <!-- Sets the name of the datasource to be the same as the datasource name in quarkus.properties-->
            <property name="hibernate.connection.datasource" value="user-store" />
            <property name="jakarta.persistence.transactionType" value="JTA" />
            <property name="hibernate.hbm2ddl.auto" value="update" />
            <property name="hibernate.show_sql" value="true" />
        </properties>
    </persistence-unit>
</persistence>
```

   - pom.xml 파일은 maven 설정 파일이다. 이 파일에서 눈여겨 봐야 할 것은 Keycloak에서 참조해야 하는 라이브러리 이다. maven에는 keycloak에서 제공하는 라이브러리가 상당히 많은데, 목적에 따라서 참조해야 하는 라이브러리가 다르다 (이 부분을 명확하게 설명한 문서가 없어서 필자도 많이 헤맸다)

```
<properties>
    	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    	<maven.compiler.source>1.8</maven.compiler.source> # 1.8 이상이어야 함
    	<maven.compiler.target>1.8</maven.compiler.target> # 1.8 이상이어야 함
</properties>
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.keycloak/keycloak-core -->
		<dependency>
    		<groupId>org.keycloak</groupId>
    		<artifactId>keycloak-core</artifactId>
    		<version>22.0.1</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.keycloak/keycloak-server-spi -->
		<dependency>
    		<groupId>org.keycloak</groupId>
    		<artifactId>keycloak-server-spi</artifactId>
    		<version>22.0.1</version>
    		<scope>provided</scope>
		</dependency>
		<dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-model-legacy</artifactId>
            <version>22.0.1</version>
            <scope>provided</scope>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.keycloak/keycloak-connections-jpa -->
		<dependency>
    		<groupId>org.keycloak</groupId>
    		<artifactId>keycloak-connections-jpa</artifactId>
    		<version>1.8.1.Final</version>
		</dependency>
    <dependency>
        <groupId>org.jboss.logging</groupId>
        <artifactId>jboss-logging</artifactId>
        <version>3.5.3.Final</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
    		<groupId>javax.persistence</groupId>
    		<artifactId>javax.persistence-api</artifactId>
    		<version>2.2</version>
		</dependency>
</dependencies>
```

3. 소스코드 설명   
- CustomUserStorageProviderFactory
  - Custom User Federation을 위해서 반드시 필요한 클래스는 Factory 클래스이다
  - 이 클래스는 META-INF/services/org.keycloak.storage.UserStorageProviderFactory 파일에서 정의된 이름과 동일한 이름을 가져야 한다
  - Keycloak의 관리자 화면에서 User Federation 메뉴를 선택/추가한다면 Keycloak은 CustomUserStorageProviderFactory 클래스의 create 함수를 호출한다.
  - 아래의 코드는 이 클래스를 구현한 예제이다  
```
import org.jboss.logging.Logger;
import org.keycloak.component.ComponentModel;
import org.keycloak.models.KeycloakSession;
import org.keycloak.storage.UserStorageProviderFactory;

// 반드시 UserStorageProviderFactory를 implements 해야 한다
public class CustomUserStorageProviderFactory implements UserStorageProviderFactory<CustomUserStorageProvider> {
	
	public static final String PROVIDER_ID = "custom-user-storage-jpa";
	
	private static final Logger logger = Logger.getLogger(CustomUserStorageProviderFactory.class);

	@Override
	public CustomUserStorageProvider create(KeycloakSession session, ComponentModel model) {
    // provider를 반드시 생성해서 리턴해야 한다
		return new CustomUserStorageProvider(session, model);
	}

	@Override
  public String getId() {
      return PROVIDER_ID;
  }

  @Override
  public String getHelpText() {
      return "JPA Custom User Storage Provider";
  }

  @Override
  public void close() {
      logger.info("<<<<<< Closing factory");
  }
}
```

- CustomUserStorageProvider
  - Factory 클래스에서 생성하는 인스턴스의 대상이 되는 클래스이다
  - 아래의 코드는 이 클래스를 구현한 예제이다

```
// 목적에 따라 여러 가지 정류의 Provider를 implements 할 수 있다. 아래코드의 경우에는 사용자등록/조회/검색/비밀번호검증을 모두 포함하는 경우이다.
public class CustomUserStorageProvider implements 
		UserStorageProvider, 
		UserLookupProvider,
		UserRegistrationProvider,
    UserQueryProvider,
		CredentialInputValidator, 
		CredentialInputUpdater {
	
	private static final Logger logger=Logger.getLogger(CustomUserStorageProvider.class);
    public static final String PASSWORD_CACHE_KEY=CustomUserAdapter.class.getName()+".password";

    protected EntityManager em;

    protected ComponentModel model;
    protected KeycloakSession session;
	
	public CustomUserStorageProvider() {}
	
	public CustomUserStorageProvider(KeycloakSession session, ComponentModel model) {
		this.model=model;
		this.session=session;
		
		em=session.getProvider(JpaConnectionProvider.class, "user-store").getEntityManager();
	}
	
	@Override
    public void preRemove(RealmModel realm) {
		logger.debug("preRemove, realm name is '"+realm.getName()+"'");
    }
	
	@Override
    public void preRemove(RealmModel realm, GroupModel group) {
		logger.debug("preRemove, realm name: '"+realm.getName()+"', group name: '" + group.getName()+"'");
    }
	
	@Override
	public void preRemove(RealmModel realm, RoleModel role) {
		logger.debug("preRemove, realm name: '"+realm.getName()+"', role name: '" + role.getName()+"'");
    }
	
	@Override
	public void close() {
		logger.debug("CustomUserStorageProvider closed.");
	}
	
	@Override
	public UserModel getUserById(RealmModel realm, String id) {
		logger.info("getUserById: " + id);
		String persistenceId = StorageId.externalId(id);
		CustomUserEntity entity = em.find(CustomUserEntity.class, persistenceId);
		if (entity == null) {
			logger.info("could not find user by id: " + id);
			return null;
		}
		return new CustomUserAdapter(session, realm, model, entity);
	}
	
	@Override
	public UserModel getUserByUsername(RealmModel realm, String username) {
		logger.info("getUserByUsername: " + username);
		TypedQuery<CustomUserEntity> query=em.createNamedQuery("getUserByUsername", CustomUserEntity.class);
		query.setParameter("username", username);
		List<CustomUserEntity> result=query.getResultList();
		if (result.isEmpty()) {
			logger.info("could not find username: " + username);
			return null;
		}

		return new CustomUserAdapter(session, realm, model, result.get(0));
	}
	
	@Override
    public UserModel getUserByEmail(RealmModel realm, String email) {
        TypedQuery<CustomUserEntity> query = em.createNamedQuery("getUserByEmail", CustomUserEntity.class);
        query.setParameter("email", email);
        List<CustomUserEntity> result=query.getResultList();
        if (result.isEmpty()) return null;
        return new CustomUserAdapter(session, realm, model, result.get(0));
    }
	
	@Override
    public UserModel addUser(RealmModel realm, String username) {
		CustomUserEntity entity = new CustomUserEntity();
        entity.setId(UUID.randomUUID().toString());
        entity.setUsername(username);
        em.persist(entity);
        logger.info("added user: " + username);
        return new CustomUserAdapter(session, realm, model, entity);
    }

    @Override
    public boolean removeUser(RealmModel realm, UserModel user) {
        String persistenceId = StorageId.externalId(user.getId());
        CustomUserEntity entity = em.find(CustomUserEntity.class, persistenceId);
        if (entity == null) return false;
        em.remove(entity);
        return true;
    }

    @SuppressWarnings("unchecked")
	public void onCache(RealmModel realm, CachedUserModel user, UserModel delegate) {
        String password = ((CustomUserAdapter)delegate).getPassword();
        if (password != null) {
            user.getCachedWith().put(PASSWORD_CACHE_KEY, password);
        }
    }

    @Override
    public boolean supportsCredentialType(String credentialType) {
        return PasswordCredentialModel.TYPE.equals(credentialType);
    }

    @Override
    public boolean updateCredential(RealmModel realm, UserModel user, CredentialInput input) {
        if (!supportsCredentialType(input.getType()) || !(input instanceof UserCredentialModel)) return false;
        UserCredentialModel cred = (UserCredentialModel)input;
        CustomUserAdapter adapter = getUserAdapter(user);
        adapter.setPassword(cred.getValue());

        return true;
    }

    public CustomUserAdapter getUserAdapter(UserModel user) {
        if (user instanceof CachedUserModel) {
            return (CustomUserAdapter)((CachedUserModel) user).getDelegateForUpdate();
        } else {
            return (CustomUserAdapter) user;
        }
    }

    @Override
    public void disableCredentialType(RealmModel realm, UserModel user, String credentialType) {
        if (!supportsCredentialType(credentialType)) return;

        getUserAdapter(user).setPassword(null);

    }

    @Override
    public Stream<String> getDisableableCredentialTypesStream(RealmModel realm, UserModel user) {
        if (getUserAdapter(user).getPassword() != null) {
            Set<String> set = new HashSet<>();
            set.add(PasswordCredentialModel.TYPE);
            return set.stream();
        } else {
            return Stream.empty();
        }
    }

    @Override
    public boolean isConfiguredFor(RealmModel realm, UserModel user, String credentialType) {
        return supportsCredentialType(credentialType) && getPassword(user) != null;
    }

    @Override
    public boolean isValid(RealmModel realm, UserModel user, CredentialInput input) {
        if (!supportsCredentialType(input.getType()) || !(input instanceof UserCredentialModel)) return false;
        UserCredentialModel cred = (UserCredentialModel)input;
        String password = getPassword(user);
        return password != null && password.equals(cred.getValue());
    }

    public String getPassword(UserModel user) {
        String password = null;
        if (user instanceof CachedUserModel) {
            password = (String)((CachedUserModel)user).getCachedWith().get(PASSWORD_CACHE_KEY);
        } else if (user instanceof CustomUserAdapter) {
            password = ((CustomUserAdapter)user).getPassword();
        }
        return password;
    }

    @Override
    public int getUsersCount(RealmModel realm) {
        Object count = em.createNamedQuery("getUserCount").getSingleResult();
        return ((Number)count).intValue();
    }

	@Override
	public Stream<UserModel> searchForUserStream(RealmModel realm, Map<String, String> params, Integer firstResult, Integer maxResults) {
		String search = params.get(UserModel.SEARCH);
        TypedQuery<CustomUserEntity> query = em.createNamedQuery("searchForUser", CustomUserEntity.class);
        query.setParameter("search", "%" + search.toLowerCase() + "%");

        if (firstResult != null) {
            query.setFirstResult(firstResult);
        }

        if (maxResults != null) {
            query.setMaxResults(maxResults);
        }

        return query.getResultStream().map(entity -> new CustomUserAdapter(session, realm, model, entity));
	}
	
	@Override
	public Stream<UserModel> getGroupMembersStream(RealmModel realm, GroupModel group, Integer firstResult, Integer maxResults) {
		return Stream.empty();
	}

	@Override
	public Stream<UserModel> searchForUserByUserAttributeStream(RealmModel realm, String attrName, String attrValue) {
		return Stream.empty();
	}
}
```

- CustomUserAdapter
  - CustomUserStorageProvider 클래스에서 생성 대상이 되는 Entity의 Adapter 클래스이다.
  - Entity 클래스를 직접 Handling 하지 않고 Adapter 클래스를 통해서 처리하는 개념이다.
  - 아래의 코드는 이 클래스를 구현한 예제이다  

```
// AbstractUserAdapterFederatedStorage 클래스는 UserModel의 서버 인터페이스이다.
public class CustomUserAdapter extends AbstractUserAdapterFederatedStorage {
	
	private static final Logger logger = Logger.getLogger(CustomUserAdapter.class);
    protected CustomUserEntity entity;
    protected String keycloakId;

	public CustomUserAdapter(KeycloakSession session, RealmModel realm, ComponentModel storageProviderModel, CustomUserEntity entity) {
		super(session, realm, storageProviderModel);
		this.entity=entity;
		this.keycloakId=StorageId.keycloakId(storageProviderModel,  entity.getId());
	}
	
	public String getPassword() {
        return entity.getPassword();
    }

    public void setPassword(String password) {
        entity.setPassword(password);
    }

	@Override
	public String getUsername() {
		return entity.getUsername();
	}

	@Override
	public void setUsername(String username) {
		entity.setUsername(username);
	}
	
	@Override
    public void setEmail(String email) {
        entity.setEmail(email);
    }

    @Override
    public String getEmail() {
        return entity.getEmail();
    }

    @Override
    public String getId() {
        return keycloakId;
    }

    @Override
    public void setSingleAttribute(String name, String value) {
        if (name.equals("phone")) {
            entity.setPhone(value);
        } else {
            super.setSingleAttribute(name, value);
        }
    }

    @Override
    public void removeAttribute(String name) {
        if (name.equals("phone")) {
            entity.setPhone(null);
        } else {
            super.removeAttribute(name);
        }
    }

    @Override
    public void setAttribute(String name, List<String> values) {
        if (name.equals("phone")) {
            entity.setPhone(values.get(0));
        } else {
            super.setAttribute(name, values);
        }
    }

    @Override
    public String getFirstAttribute(String name) {
        if (name.equals("phone")) {
            return entity.getPhone();
        } else {
            return super.getFirstAttribute(name);
        }
    }

    @Override
    public Map<String, List<String>> getAttributes() {
    	logger.debug("");
        Map<String, List<String>> attrs = super.getAttributes();
        MultivaluedHashMap<String, String> all = new MultivaluedHashMap<>();
        all.putAll(attrs);
        all.add("phone", entity.getPhone());
        return all;
    }

    @Override
    public Stream<String> getAttributeStream(String name) {
    	logger.debug("");
        if (name.equals("phone")) {
            List<String> phone = new LinkedList<>();
            phone.add(entity.getPhone());
            return phone.stream();
        } else {
            return super.getAttributeStream(name);
        }
    }
}
```

- CustomUserEntity
  - 실제 데이터베이스를 통해서 User 정보를 처리하는 클래스이다. 
  - 예제에서는 javax..persistence를 적용하였지만, hibernetes에서 제공하는 기술을 적용하거나 또는 직접 SQL를 통해 처리해도 무방하다
  - 아래의 코드는 이 클래스를 구현한 예제이다
    
```
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.NamedQueries;
import javax.persistence.NamedQuery;
import javax.persistence.Table;

@NamedQueries({
    @NamedQuery(name="getUserByUsername", query="select u from CustomUserEntity u where u.username = :username"),
    @NamedQuery(name="getUserByEmail", query="select u from CustomUserEntity u where u.email = :email"),
    @NamedQuery(name="getUserCount", query="select count(u) from CustomUserEntity u"),
    @NamedQuery(name="getAllUsers", query="select u from CustomUserEntity u"),
    @NamedQuery(name="searchForUser", query="select u from CustomUserEntity u where " +
            "( lower(u.username) like :search or u.email like :search ) order by u.username"),
})
@Entity
@Table(name="USER")
public class CustomUserEntity {

	@Id
    private String id;
    private String username;
    private String email;
    private String password;
    private String phone;
    
	public String getId() {
		return id;
	}
	
	public void setId(String id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getPhone() {
		return phone;
	}

	public void setPhone(String phone) {
		this.phone = phone;
	}
}
```

4. 패키징 및 배포
- Standalone 환경에서의 배포
  - maven으로 jar 파일을 패키징한 후에, quarkus.properties 파일을 keycloak_home/conf 디렉토리에 복사한다
  - jar 파일을 keycloak_home/providers 디렉토리에 복사한다
  - keycloak를 다시 시작한다
- Kubernetes 환경에서의 배포
  - kubernetes 환경의 경우 배포가 좀 까다롭다. 그 이유는 컨테이너 이미지에 우리가 만든 jar와 quarkus.properties 파일이 컨테이너 이미지에 포함되어 있어야 하기 때문이다.
  - 컨테이너 이미지를 생성할 때에 docker 파일에서 quarkus.properties 파일을 /opt/bitnami/keycloak/conf 디렉토리에 복사한다. (디렉토리 위치는 base 이미지에 따라서 달라질 수 있다)  
  - 컨테이너 이미지를 생성할 때에 docker 파일에서 패키징된 jar 파일을 /opt/bitmani/keycloak/providers 디렉토리에 복사한다. (디렉토리 위치는 base 이미지에 따라서 달라질 수 있다)
  - Zenkins를 활용한 CICD 환경에서 Jenkinsfile의 스크립트로 작성해 놓으면 됨

5. 결과 확인
- 관리자 화면의 User federation 메뉴를 클릭했을 대에 아래 그림처럼 Custom-user-storage-jpa providers 라는 선택버튼이 나타난다면 성공이다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/9e73c1cb-8d10-4d24-931d-d920c1fde8a0)  




