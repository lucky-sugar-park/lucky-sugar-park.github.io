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
<br/>
> Keycloak은 ID와 접근관리 기능을 제공하는 오픈소스이다. OAuth2, SAML2 표준 규격을 지원하고 외부 IDP와 연계해서 SSO를 제공이 가능하다.   
> 또한 Open ID Connect 표준규격으로 자체 사용자 ID를 관리할 수 있으며, 이미 존재하는 사용자 정보 Storage와 연계할 수도 있다.   
> Windows, Linux 환경과 Standalone, Docker, K8S 환경을 지원하기 때문에 활용 범위가 넓다.  
> 어지간 하면 별도의 코딩 없이 (제공되는) 관리자 화면만으로도 기업 또는 어플리케이션에서의 사용자 인증 및 권한관리 기능을 수행할 수 있지만,   
> 기능의 확장 또는 기존 시스템과의 연계 또는 활용 등이 필요할 경우에는 Keycloak 기능 확장을 위해서 추가 개발이 필요할 수도 있다.
> [공식 개발자 사이트](https://www.keycloak.org/docs/latest/server_development/)에 가이드가 자세하게 나와 있으나,   
> 모든 것이 그렇듯 (환경차이 등으로) 문서대로 되지 않는 부분들이 꽤 있다.   
> 이번 블로그에서는 [공식 개발자 사이트](https://www.keycloak.org/docs/latest/server_development/)에 있는 내용들을 (실제 예제와 함께)   
> 좀 더 쉽게 설명하고자 한다 (한번에 다 할 수는 없고, 지속적/점증적으로 조금씩 내용을 추가할 계획이다)

<br/>
[서버개발자가이드](https://www.keycloak.org/docs/latest/server_development/)를 보면 아래의 그림과 같은 내용들이 있다.  
<br/>
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d847d91a-eb3a-42ee-a1ee-9c4b3cb7e473)   

대충 살펴보면 Keycloak에서 제공하는 관리자용 REST API를 사용하는 방법, Custom 테마적용 또는 테마선택하는 방법, Custom User Storage 추가하는 방법 등,   
개발자가 Keycloak과의 연계 또는 Keycloak Plugin을 만드는 방법 등을 설명하고 있다.  

이번에는 그 중에서 가장 유용하고 필요할 것으로 판단되는 (기존 DBMS에 저장된) 사용자 정보를 활용하는 방법에 대해서 설명한다.   
<br/>
#### User Storage SPI
User Storage SPI는 사용자정보를 담고 있는 (이미 존재하는) Storage (DBMS 또는 File)를 재사용해서 (Keycloak에서) 사용자 인증 및 인가를 구성하기 위함이다.   
쉽게 말해서 Keycloak이 인식할 수 있는 형태의 플러그인 (jar 파일)을 "Keycloak_HOME/providers" 라는 디렉토리에 넣어주고 keycloak을 구동하게 되면,  



