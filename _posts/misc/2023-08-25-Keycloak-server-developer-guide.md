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

3. 소스코드 설명


4. 패키징 및 배포
- Standalone 환경에서의 배포
- Kubernetes 환경에서의 배포   

5. 결과 확인




