---
title: "How to implement Micro frontend architecture with Reactjs, Nextjs, Nginx and Minio"  
last_modified_at: 2023-08-05T20:58:10-05:00
categories:
  - Blog
tags:
  - Micro frontend
  - Next.js
  - React.js
  - Nginx
  - Minio
---

> 마이크로 서비스 아키텍처 도입으로 Backend side는 어느정도 성숙해진 느낌이지만,
> Frontend 쪽은 여전히 모노리스 아키텍처에 의해 (많은 사람들이 모두 알고 있는) 문제점들 때문에 개발자-운영자들이 고통받고 있다. 
> 2020년 webpack 5 의 module federation 기술이 소개된 이후에 Frontend side에서도 Micro frontend 아키텍처를 적용할 수 있는 기술적 기반이 마련되었지만,
> 아직까지는 현장에서의 성공적인 도입사례가 잘 안 보인다.
> 
> 한편 Micro frontend와 다른 한 축으로, Server side rendering (이하 SSR) 의 필요성이 강하게 대두되고 있으며
> 이미 Next, Nust 등의 프레임워크가 소개되었고 React.js나 Vue.js를 기반으로 SSR을 적용할 수 있는 기술적 기반이 마련된 상태이지만,
> 이 역시 마찬가지로 성공적인 도입사례를 찾기가 쉽지 않은 것 같다.
>
> 가만히 생각해보면 위의 두 가지 기술 Trend는 각각이 아닌 통합된 형태의 아키텍처로 (특히 엔터프라이즈 환경에서는) 처리 되어야 할 필요성이 있다
> (물론 소규모의 frontend 환경에서는 Micro frontend 아키텍처의 필요성을 상대적으로 느끼지 못할 수 있을 수도 있다) 
>
> 이번 글에서는 이 두 가지 요소의 통합된 아키텍처를 연구해 보고, 파일럿 형태로 구현해 보고자 한다.
> (상대적으로 능숙한 React.js를 적용해 보겠다. 하지만 Micro frontend의 특징이기도 하지만, 각각의 Micro frontend 별로 개발팀이 편한 기술을 적용하면 된다)
>
> 이를 통해서 Micro frontend 아키텍처에 대한
> - 독립적인 Frontend 기준 (Frontend 알갱이 크기) 
> - 프로젝트의 Directory 구조, 파일이름, 코딩 스타일, 설계패턴
> - 배포위치와 방식
> 등을 고민해 보고, 의미 있는 결론을 도출해 보고자 한다

## 순서
1) 사용 시나리오  
2) 목표 시스템 구성  
2-1) 파일럿 시스템 구성도와 간단한 설명  
2-2) (아주 간단한) 사용자 화면 스케치  
3) 설치  
3-1) NODE.JS 설치  
3-2) MINIO 설치  
3-3) NGINX 설치  
4) 구현  
4-1) 각 영역별 구현  
5) 테스트  
5-1) 시나리오에 대한 테스트 및 동작 확인  
6) Key Findings  
7) 에필로그

## 사용 시나리오  
다음과 같은 시나리오를 대상으로 시스템을 구성해 보겠다.  
<br/>
1) 사용자가 http://localhost:80으로 접속한다  
2) 웹 브라우저에 사용자 화면을 보여준다. 화면은 Header, Body, Footer로 구성되며 Body는 Left Panel과 Menu 영역과 Content 영역으로 나누어진다 ( Header, Footer, Body - Left Panel, menu, Content )   
3) Header와 Footer 부분에는 공통 Micro frontend 인 Alert, Notification, User Profile, Search, Bookmark, Breadcrumb Navigation, Copyright Component를 Bucket으로부터 가져와서 보여준다  
4) 초기화면은 Contents 부분에 Home 화면을 보여준다 (단순히 Home 화면임을 표시)  
5) 사용자가 Menu에서 “사용자목록”을 선택하면 CSR 방식의 Micro frontend Component를 Next 서버에서 받아와서 Content 부분에 보여준다  
6) 사용자가 Menu에서 “게시글목록”을 선택하면 SSR 방식의 Micro frontend Component를 Next 서버에서 받아와서 Content 부분에 보여준다  
7) 기타 나머지 Menu를 클릭하면 Container와 함께 Load 되었었던 CSR 방식의 Component를 Content 부분에 보여준다  

## 목표 시스템 구성  
위 시나리오를 제공하기 위한 목표 시스템은 아래 그림과 같다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/be6171d4-5235-4e82-b217-21f09e7e427b)  

UI의 경우  Client (웹 브라우저)에서 필요한 UI Page를 동적으로 Binding 및 통합 구성할 수 있도록 구성하겠다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/45d83add-6f45-46f2-85b9-f0526120d78f)  

사용자 화면은 개략적으로 (Micro frontend 개념을 보여줄 수 있도록) 아래의 그림처럼 구현해 보겠다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/3ee85457-389f-4c87-9ab1-e87c51cc86c1)  

대상이 되는 마이크로 프론트엔드는 아래과 같다.  
- Container, Common, Biz-Next  
- Container
  - 화면을 구성한다. Common, Biz-Next 에서 구현된 Micro Frontend 들을 동적으로 가져다가 사용한다.
- Common
  - User Profile, Search, Bookmark, Breadcrumb Navigation, Copyright를 제공함
  - Object Storage (Minio)에 올려져 있으, 웹 브라우저에서의 요청에 의해서 해당 Micro Frontend를 서비스 함
- Biz-Next
  - Next.js 프레임워크 기반의 Micro Frontend 제공 (UserList, BoardList)
  - SSR 방식 Micro Frontend (BoardList)과 CSR 방식 Micro Frontend (UserList) 제공

## 설치
#### Node.js 설치 (OS: Windows) 
- https://offbyone.tistory.com/441 참조 

#### Minio 설치 (OS: Windows)
- 다운로드 (https://min.io/download#/windows)  
- 적절한 곳에 실행파일을 옮긴 후에 실행하면 됨  
- 실행방법: minio.exe server D:\Programs\minio\storage --console-address :9090   

#### Nginx 설치 (OS: Windows)  
- 오픈소스 다운로드 (http://nginx.org/en/download.html) 및 압축해제  
- NGINX는 오픈소스와 상용이 있음 (Nginx Plus: https://nginxstore.com/store/products/nginx-plus/)  
- 오픈소스는 상용대비 기능 제약이 있음 (https://nginxstore.com/store/products/nginx-plus/)  
- 실행: 실행파일 더블클릭 or (nginx.exe start, nginx.exe –s stop, nginx.exe –s quit, nginx.exe –s reload, nginx.exe –s status)

#### GIT 설치 (OS: Windows) 
- 다운로드 및 설치 (https://git-scm.com/book/ko/v2/시작하기-Git-설치)  
- GIT 사용법 (https://velog.io/@backtony/git-사용법-정리 참조)

## 구현 및 설정
#### common Component 프로젝트 
편의상 GIT Repository에 올려 놓은 소소코드를 설명함 
(최초 프로젝트는 ```npx create-mf-app common``` 명령어로 생성하였으나, 기존 프로젝트를 활용할 때는 불필요)  

1) pilot이라는 디렉토리 생성 및 pilot 디렉토리로 이동  
2) 소스코드 다운로드: ```git clone <git repository 주소>```  
3) 설치  
   - 디렉토리 이동: ```cd common```  
   - 설치: ```npm install –save```  
   - 필요한 라이브러리를 다운로드 하지 못한다면 사내 프록시나 방화벽 점검 필요  
4) Micro frontend 컴포넌트 컴파일  
   - ```npm run build``` 명령어 수행 (package.json 파일에 정의된 스크립트 이름)  
   - 빌드결과는 dist라는 이름의 디렉토리에 생성됨  
5) Micro frontend 컴포넌트 업로드 (To Minio)  
   - 웹 브라우저로 ```http://localhost:9000``` 접속 후에 Pilot이라는 이름의 Bucket 생성하고 common Path 생성 후에 dist 디렉토리에 있는 모든 파일을 업로드 함  

6) webpck.config.js 설명
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/ec93ca3f-9052-4b08-99d9-1fe769839bc6)

7) package.json 설명
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/a56ecdd1-afc4-433b-993b-17e4cc18351f)

8) Minio에 업로드된 결과
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/bcc450d0-f6e9-46fc-9ea5-a3a1eaa9e304)

#### biz-next 프로젝트  
최초 프로젝트는 ```npx create-next-app biz-next``` 명령어로 생성하였으나, 기존 소스코드를 활용할 때에는 설치하기만 하면 됨  
1) 생성된 pilot이라는 디렉토리로 이동 (없으면 디렉토리 생성)  
2) 소스코드 다운로드: ```git clone <git repository 주소>```  
3) 설치
   - 디렉토리 이동: ```cd biz-next```
   - 설치: ```npm install –save```
   - 필요한 라이브러리를 다운로드 하지 못한다면 사내 프록시나 방화벽 점검 필요
4) next.config.js 파일 수정
   - Remote Host로 (Container 프로젝트) 노출할 Micro frontend Component 정의
   - Common Component로부터 Copyright 재사용을 위한 remote Component 정의
5) Next 서버 구동
   - npm run dev 명령어 수행 (package.json 파일에 정의된 스크립트 이름)
   - .next/static이라는 디렉토리에 외부로 노출할 Micro frontend component가 저장됨 (remoteEntry.js)을 확인함

6) package.json 설명
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/5b97832f-0d9f-4098-a6c1-19b8d72d11b4)

7) next.config.js 설명  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/c482d677-5b35-4dc5-9275-dbf86f686510)

8) Next에서 동적으로 공통 컴포넌트를 가져오는 방법
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/8681f9d5-6bde-4c56-bcf0-f82c3ac8e05f)

9) 서버 구동결과  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/2bba51ec-0e0f-4ff1-8ade-55e4ef7f85cf)

#### Container 프로젝트  
최초 프로젝트는 ```npx create-mf-app container``` 명령어로 생성하였으나, 기존 소스코드를 활용할 때에는 설치하기만 하면 됨  
1) 생성된 pilot이라는 디렉토리로 이동 (없으면 디렉토리 생성)
2) 소스코드 다운로드: ```git clone <git repository 주소>```  
3) 설치
   - 디렉토리 이동: ```cd container```
   - 설치: ```npm install –save```
   - 필요한 라이브러리를 다운로드 하지 못한다면 사내 프록시나 방화벽 점검 필요
4) webpack.config.js 파일 수정
   - Common Component로부터 Copyright 재사용을 위한 remote Component 정의
   - biz-next Component로부터 UserList 재사용을 위한 remote Component 정의
5) Remote로부터 Micro frontend Component 재사용 방식의 코딩
6) container 서버 구동
   - ```npm run start``` 명령어 수행 (package.json 파일에 정의된 스크립트 이름)

7) package.json 설명  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/125a0767-c1ff-41d0-baa1-85845747e6db)

8) webpack.config.js 설명  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/00f01333-302c-47e4-b910-13d5bb1426e1)

9) React에서 Remote Component import 하는 방법
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/cd89c066-29ba-4025-8b3d-d2b5659f3ceb)

10) 서버 구동결과
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/be57fa6b-c4c6-450a-8dc9-dd880c4fe11e)

#### 테스트 및 동작확인  
1) 웹 브라우저로 http://localhost 를 입력한다. 아래의 그림처럼 나오면 성공이다  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/7e9c01ec-4262-4a9f-9f3d-6a7cb8ccf935)

2) Nginx의 설정과 웹서버 로그를 확인해 보자  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/ccfd40e8-0922-4e31-87d0-0958a1f461db)

3) 메뉴에서 기술인 목록을 클릭해 보자
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/211948c5-918e-4f20-8a31-0524ecddc08e)

4) 메뉴에서 게시글 목록을 클릭해 보자
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/16e21bf5-2cdf-4258-a301-571d1265269d)

## Key Findings  
이번 파일럿을 통해서 발견한 주요 의미 있는 것들을 기술한다  
1) SSR (Server Side Rendering)은 URI를 통해서 호출한 경우에만 동작함  
   - Remote로 정의한 후에 import 하면 모두 CSR로 동작함 
   - 조금만 더 생각해보면 당연한 것임 (과거 JSP나 ASP, PHP를 생각해봐도 마찬가지임)  
2) React에서 SSR을 참조하는 방법  
    - useEffect 함수 내에서 URI를 호출한 후에 결과로 넘어온 HTML을 innerHTML로 주는 방법 (코드참조)  
    - iframe을 사용하는 방법 (코드참조)  
3) SSR 컴포넌트와 통신하는 방법  
    - onLoad 이벤트에 함수를 걸어놓는 방법 (코드참조)  
    - window의 message 이벤트에 함수를 걸어 놓는 방법 (코드참조)  
    - 두 가지 방법모두 만족스럽지 못했음 (이 부분은 좀 더 많은 연구가 필요한 부분임)  
4) Remote 컴포넌트를 사용할 때에는 lazy함수 + Suspense 요소를 적용하는 것이 안전하다  
    - Suspense 요소에 정의되어 있는 fallback 함수를 통해서 사용자에게 상태를 알려줄 수 있음 (코드참조)  
5) 적당한 알겡이 크기의  Micro frontend 컴포넌트

<br/>
> 이번에도 용두사미가 된 듯한 느낌이다. 제법 큰 생각을 가지고 시작했으나, 여러가지 이유로 (귀차니즘이 가장 큰 이유 같다) 처음에 생각했었던 바를 모두 표현하지 못했다.  
> 하지만, 나름 의미있는 과정이었고 막연하게 알고 있었던 Micro frontend + SSR 아키텍처에 대해서 좀 더 구체적으로 판단할 수 있었다  
>
> 이 아키텍처가 아직까지는 모범사례를 찾아보기가 쉽지 않은데 (알려지지 않은 것일 수도 있음) 아마도 아래와 같은 몇 가지 이유가 있을 수도 있겠다  
> - 새로운 아키텍처에 적합한 잘 정제된 Coding Convention 이 없다
> - 나중에 합치기 위해서는 어느 정도 개발자간 Contract이 필요하다
> - Micro Frontend 알겡이 크기를 어떤 단위로 할 것인가?
>
> Micro Frontend 아키텍처에 적합한 Coding Convention과 아키텍처 패턴을 정리해 보고 싶다.
> 분명히 Micro Frontend 아키텍처는 기업의 Business Agility 향상에 중요한 부분이라는 확신이 있기 때문에,
> 충분히 투자할 만한 가치가 있다고 생각한다.
>
> 머지 않은 미래에 정리가 되었으면 좋겠다.  
