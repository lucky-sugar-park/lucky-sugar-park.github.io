---
title: "Micro frontend with react"
last_modified_at: 2023-06-15T10:46:02-05:00
categories:
  - Blog
tags:
  - Micro frontend
  - React
---

아래 글을 번역 했습니다.  
[Building a micro-frontend application with eact](https://blog.logrocket.com/build-micro-frontend-application-react/)

대규모 프로젝트에서 일하면서 그들의 코드베이스를 잘 관리하는 것은 쉬운 일이 아니다.  
비록 한 동안 마이크로 프론트엔드가 관심을 받아왔지만, 마이크로 프론트엔드의 독창적 특징 때문에 여전히 관심은 증가하고 있다  

마이크로 프론트엔드 방식을 적용하면, 여러 개의 개발팀들이 서로의 모듈에 영향을 미치지 않으면서 동일한 프로젝트에서 각자의 모듈을 개발하는 방식으로 일할 수 있다.  
현재 시스템에 얼마나 많은 모듈이 추가될 것인가와 같은 것들은 문제가 되지 않는다.  

이 글에서는 마이크로 프론트엔드의 기본에 대해서 살펴보고 React를 적용해서 그 개념을 어떻게 구현할 것인지를 보고자 한다.  
또한 마이크로 프론트엔드를 사용하면 얻게 되는 이득에 대해서 논의해 보고자 한다.  

다음과 같은 내용으로 기술한다.
- 마이크로 프론트엔드 소개
- 마이크로 프론트엔드의 모범사례
- 마이크로 프론트엔드를 적용함으로써 얻게 되는 이득
- 사전조건
- 마이크로 프론트엔드 시작하기  
- 아미크로 프론트엔드 구현하기
- 모듈 추가하기

## 마이크로 프론트엔드 소개  
현재 대부분의 웹 어플리케이션 개발은 모노리스 프론트엔드 방식으로 구축되고 있고, 이 방식이 마이크로 서비스의 주류를 이루고 있다. 그러나 서로 다른 팀들의 개발자들이 이 모노리스 방식의 프론트엔드 개발을 함께 진행하면서 유지보수 등의 어려움이 증가하게 된다. 프론트엔드에서도 마이크로 서비스 개념을 도입함으로써 이러한 문제를 해결할 수 있다.  

**마이크로 프론트엔드라는 용어는 마이크로 서비스의 개념을 프론트엔드로 확장**한 것이다. 마이크로 프론트엔드의 목적은 (필수적으로) 서로다른 미션을 가진 독립된 팀들이 소유한 프론트엔드 모듈의 집합으로써 웹 어플리케이션을 다루는 것이다. 각 팀들은 (예컨데) UI부터 데이터베이스까지 처음부터 끝까지 자신의 모듈을 책임지게 된다.  

마이크로 프론트엔드는 어느 특별한 구조를 따르는 것도 아니며 정형화된 경계가 있는 것도 아니다. 시간이 지나면서 당신의 프로젝트는 진화할 것이고, 당신의 마이크로 프론트엔드를 보완할 필요가 있을 수도 있다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d340238b-3b95-4a16-ba80-f541e6964265)  
Image Source: [https://microfrontends.com/](https://microfrontends.com/)  

React는 인기 있는 프론트엔드 기술 스텍이기 때문에 마이크로 프론트엔드를 구현함에 있어서 좋은 선택지가 될 수 있다. 마이크로 프론트엔드 아키텍처는 아직까지는 새로운 개념이고 다른 프레임워크들에 의해서 채택되고 있는 중이다. (모범사례 같은 것들도 아직은 진행중이라고 할 수 있다) 때문에 Scratch로부터 마이크로 프론트엔드를 구현해 보는 것도 도전적이라는 것을 깨닫게 될 것이다. [create-mf-app](https://github.com/jherr/create-mf-app) 를 참조해 보자.  

위 링크의 문서를 보면 [create-mf-app](https://github.com/jherr/create-mf-app) 에서는 module federation application, API server 또는 템플릿 기반의 라이브러리를 생성한다. 프레임워크 불가지론 (Agnostic) 같은데, Preact, React, Svelte 등과 같은 여러 개의 프레임워크를 지원함을 의미한다.  

## 마이크로 프론트엔드 모범사례  
마이크로 프론트엔드 아키텍처를 구현할 때에 명심해야 할 몇 가지 모범사례를 살펴볼 것이다.  

#### 팀코드 분리  
각 팀은 자신의 기능을 독립적인 어플로 개발해야 한다. 이 때, 글로벌 변수에 종속적인 공유상태를 사용하면 안된다.  
결과적으로 모든 팀이 동일한 프레임워크를 사용한다고 하더라도, 런타임 시에 공유되는 것이 없어야 한다.  

#### 팀 접두어  
아직 팀간 완전한 분리가 어렵다면, 팀들은 자신들만의 네임스페이스를 상호 합의하에 사용해야 한다.  
이는 CSS, 이벤트, 로컬 스토리즈 등에 있어서 발생할 수 있는 충돌을 피하기 위함이다.

#### 회복력이 강한 웹 어플리케이션을 만들어라  
각 독립적 팀은 자신의 기능을 개발할 때에 회복력이 강하도록 기능을 개발해야 한다. 자바 스크립트가 불가하거나 또는 실패하더라도 그 기능은 동작해야 한다.  
추가적으로 Universal rendering과 Progressive enhancement를 통한 성능향상을 고려해야 한다.  

#### Custom API 보다는 native browser API를 사용하라 
팀 모듈간 커뮤니케이션을 할 때에는 global pub/sub 시스템을 만드는 대신에 브라우저 이벤트를 사용해야 한다.  
팀 모듈간 커뮤니테이션을 위한 API는 가능하면 간단함을 유지해야 한다  

## 마이크로 프론트엔드의 사용 이점  
마이크로 프론트엔드 웹 어플리케이션을 보다 유지보수가 편리하도록 만드는 것이 좋다.   
큰 어플리케이션의 일부 파트를 개발해 본 개발자라면 잘 알겠지만, 모든 것을 관리하는 것은 매우 굼뜬 일이다.  
마이크로 프론트엔드 방식은 **분할정복 법칙과 (Devide and conquer rule)** 유사하게 동작한다.  
마이크로 프론트엔드 방식을 적용하면 아래와 같은 가치를 얻을 수 있다.  

#### 배포와 보안  
마이크로 프론트엔드 아키텍처의 중요한 이점 중 하나는 하나의 화면을 여러 개의 부분으로 나눌수 있고 이 여러 개의 부분은 독립적으로 배포될 수 있다는 것이다.  
[Vercel](https://vercel.com/blog/monorepos) (호스팅 플랫폼 제공업체)은 상이한 (언어나 프레임워크가 다를 수도 있음) 프론트엔드를 함께 배포할 수 있다.  그렇지 않으면 [Netlify](https://www.netlify.com/)와 같은 서비스를 이용할 수 있다.  일단 마이크로 프론트엔드가 배포되면, 개별적 프론트엔드로써 사용할 수 있다.  

마이크로 프론트엔드를 보호하기 위해서는 Wildcard, 단일 또는 다중 도메인 또는 SAN SSL Certificate와 같은 SSL 인증서를 사용할 수 있다.  단일 또는 다중 도메인 SSL 인증서를 사용함으로써 다중 사이트와 서브 도메인들을 보호할 수 있다.  

#### 기술 불가지론과 확장성  
마이크로 프론트엔드 아키텍처를 적용하면, 단일 프로젝트 내에서 React, Vue, Angular와 같은 어느 개발언어나 프레임워크를 엮을 수 있다. 각 프론트엔드 팀은 자신들만의 기술 스텍을 (다른 팀과 상관 없이) 고를 수 있다.  

#### 빠른 개발  
각 팀은 분리된 어플리케이션 기능을 다룬다. 새로운 개발자들에게 (모노리스 프론트엔드에 비해서) 이런 방식이 이해하기 쉽다. 결과적으로 러닝커브가 선형적이게 되고 신규 개발자들은 비용대비 높은 결과를 낼 수 있게 된다.  

#### 종적 도메인 오너쉽  
마이크로 프론트엔드가 소개되기 이전에는 종적 도메인 오너쉽은 (마이크로 서비스 아키텍처를 통해서) 백엔드에서만 가능했었다. 기업들은 독립적인 팀들 사이에서 백엔드의 오너쉽을 가져가는 방식으로 제품개발을 확장할 수 있다. 하지만 프론트엔드에서는 여전히 모노리스 방식을 유지하고 있다.  

마이크로 프론트엔드가 소개된 이후로, 프론트엔드 또한 종적 도메인 컴포넌트로 나누어지고 이는 각 팀에 의해서 오너쉽에 부여된다 (UI부터 데이터베이스까지)  

#### 코드 재사용성  
마이크로 프론트엔드를 적용함으로써 코드의 재사용성을 향상시킬 수 있다. 이는 어느 한 팀이 여러 팀에 의해서 재사용될 수 있는 컴포넌트를 개발하고 배포할 수 있기 때문이다.  

#### 쉬운 테스트  
당연한 말이지만 통합 테스트로 넘어가기 전에, 어플리케이션의 개별 부분들을 테스트하는 것이 좋다. 개발 팀들은 어플리케이션을 테스트 하기 전에 마이크로 프론트엔드를 테스트할 것이다. 이렇게 함으로써, 실제 시스템에서 버그의 가능성을 줄일 수 있다.  

이런 측면과는 별개로, **작은 코드 베이스** (쉬운 유지보수 가능)와 시스템으로부터 어떤 모듈을 빨리 추가/제거할 수 있는 능력을 확보할 수 있다.  

## 사전조건  
여기에서는 React를 사용해서 마이크로 프론트엔드를 구축해 본다. 이를 위해서는 다음의 조건을 만족해야 한다.  
<br/>
- 자바스크립트에 대한 기본 지식
- React에 대한 기본 지식
- Webpack에 대한 기본 지식
- Node.js v19가 설치되어 있어야 함

## 마이크로 프론트엔드 시작하기
micro-frontend-react라는 어플리케이션 폴더를 생성한다. 이 폴더로부터 React micro-frontend 어플리케이션을 시작하기 위해서는 ```npx create-mf-app```를 구동하고 다음의 정보를 대화형 터미널에서 입력한다.  
<br/>
- Name: ```home```
- Project type: ```Application```
- Port Number: ```3000```

React, JavaScript, Tailwind를 선택한다. ```home```이라 불리는 React 마이크로 프론트엔드 어플리케이션은 시작될 것이다.  터미널에서 다음의 그림을 볼 수 있어야 한다.  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/5bc8df61-94c8-401f-9a00-f79fbf193875)  

```home``` 폴더로부터 어플리케이션을 테스트하기 위해서는 ```yarn install```을 실행한다 (종속 라이브러리 설치), ```dev-server```를 구동하기 위해서 ```yarn start``` 명령어를 실행한다. 만약 모두 성공하면 다음의 그림이 보여야 한다. 

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/5716595d-fe03-443e-a1e8-7d76fbd8e412)

```about``` 폴더에서 다음의 데이터를 사용하여 위의 스텝을 반복한다. 
<br/>
- Name: ```about```
- Project type: ```Application```
- Port Number: `3001```

앞에서와 같이 React, Javascript 그리고 Tailwind를 선택한다.  

최종적으로 어플리케이션을 테스트하기 위해서 ```yarn install``` 명령어로 종속 라이브러리를 설치하고 ```yarn start``` 명령어로 ```dev-server```를 시작한다. 모든 것이 성공적으로 완료되면 다음의 그림이 보여야 한다.  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/a38277e2-9497-45fe-b561-9dd58fe4e8f3)

## 마이크로 프론트엔드 구현하기 
```home``` 어플리케이션은 ```Header```와 ```Footer```라는 두개의 컴포넌트를 포함하고 있다. ```about``` 어플리케이션은 이런 컴포너트들을 import 한후에 소비한다.  

이것을 구현하기 위해서는 ```home```의 ```src``` 디렉토리에서 두 개의 컴포넌트를 만든다 (```Header.jsx```와 ```Footer.jsx```)  다음의 코드를 각각 ```Header.jsx```와 ```Footer.jsx```에 붙여 넣는다.  
```
import React from "react"
export default function Header() {
    return (
        <div className="p-5 bg-blue-500 text-white -text-3xl font-bold">
            Micro Frontend Header
        </div>
    )
}
```
<br/>
```
import React from "react"
export default function Footer() {
    return (
        <div className="p-5 bg-blue-500 text-white -text-3xl font-bold">
            Micro Frontend Footer
        </div>
    )
}
```

다음으로 ```App.jsx``` 컴포넌트를 다음의 navbars를 사용하도록 업데이트 한다.  
```
import React from "react";
import ReactDOM from "react-dom";
import "./index.scss";
import Header from "./Header"
import Footer from "./Footer";

const App = () => (
  <div className="text-3xl mx-auto max-w-6xl">
    <Header />
    <div className="my-10">
      Home page Content
    </div>
    <Footer />
  </div>
);

ReactDOM.render(<App />, document.getElementById("app"));
```  

서버를 재구동하여 어플리케이션을 테스트 한다. 다음의 화면이 보여야 한다.  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/c1dd6dd4-a152-465f-8654-572345dd188a)

다음으로 ```about``` 어플리케이션아넹 있는 ```about```페이지를 빌드해야 한다. 그렇게 하기 위해서는 ```about```어플리케이션의 ```App.jsx``` 컴포넌트를 다음과 같이 수정한다.  
```
import React from "react";
import ReactDOM from "react-dom";
import "./index.scss";
const App = () => (
  <div className="text-3xl mx-auto max-w-6xl">
    <div class="text-center">
      <img
        src="https://mdbcdn.b-cdn.net/img/new/avatars/8.webp"
        class="rounded-full w-32 mb-4 mx-auto"
        alt="Avatar"
      />
      <h5 class="text-xl font-medium leading-tight mb-2">John Doe</h5>
      <p class="text-gray-500">Web designer</p>
    </div>
  </div>
);

ReactDOM.render(<App />, document.getElementById("app"));
```  

```dev-server```를 다시 구동하면 다음의 그림이 보인다.   
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/58d443a3-fd4a-4d30-bdad-cb48f6681899)  

위의 UI에서 header와 footer navigation이 필요한 것을 알 수 있다. 그러나 우리는 마이크로 프론트엔드를 통해 그것들을 공유할 수 있기 때문에 코드를 복사해서 생성할 필요는 없다.  

## 모듈 추가하기 
위에서 ```about```어플리케이션내의 ```about```페이지가 (홈 어플리케이션의 컴포넌트인) ```Header```와 ```Footer``` 컴포넌트를 소비하는 것이 필요하다고 언급하였다.  이렇게 하기 위해서는 모듈을 추가할 필요가 있다.  
<br/>
```home``` 어플리케이션의 ```Header```와 ```Footer``` 컴포넌트를 마이크로 프론트엔드로 변경해서 다른 어플리케이션의 컴포넌트들이 사용할 수 있도록 변경할 것이다.  

```home``` 어플리케이션의 ```webpack.config.js``` 파일을 연다 (```create-mf-app``` 패키지에 의해서 이미 생성 및 구성된 상태임) 첫번째로 ```ModuleFederationPlugin``` 구성내의 ```expose``` 속성을 아래의 내용으로 수정한다.  

```
exposes: {
        "./Header": "./src/Header.jsx",
        "./Footer": "./src/Footer.jsx"
      },
```  

위의 코드에서, ```home```  어플리케이션이 ```Header```와 ```Footer``` 컴포넌트를 마이크로 프론트엔드로 노출하도록 명시하였다.  결론적으로 이 컴폰넌트들은 공유될 수 있다.  

이제 서버를 재시작한다. UI상으로는 아무 것도 안 변했지만, 하나의 원격 엔트리 파일이 hood 아래에서 생성되었다. 원격 엔트리 파일을 보기 위해서는 웹 브라우저에서 ```localhost:3000/remoteEntry.js```를 입력한다. 아래의 그림처럼 보인다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/4f873ca8-6722-489f-8417-3b3bddbb6311)  

이 원격 엔트리 파일 (```remoteEntry.js```)은 ```home``` 어플리케이션에 의해서 노출되어지는 모든 모듈들의 manifest 파일이다.  

설정을 완료하기 위해서, manifest  파일의 링크 (```localhost:3000/remoteEntry.js```)를 복하한 후에 ```webpack.config.js``` 파일의 ```ModuleFederationPlugin``` 설정에서 ```remotes``` 속성을 아래처럼 수정한다.  
```
remotes: {
        home: "home@http://localhost:3000/remoteEntry.js",
      },
```  

위의 코드는 ```about``` 컴포넌트가 ```home```이라는 원격 마이크로 프론트엔드 컴포넌트를 가지고 있음을 나타낸다.  이렇게 세팅하면 ```home``` 어플리케이션에서 노출된 어떤 컴포넌트들도 접근할 수 있게 된다.  

이제, 공유된 navbars를 가지고 있는 ```about``` 어플리케이션의 ```App.jsx``` 컴포넌트를 아래처럼 수정한다.  
```
import React from "react";
import ReactDOM from "react-dom";
import "./index.scss";
import Header from "home/Header";
import Footer from "home/Footer";
const App = () => (
  <div className="text-3xl mx-auto max-w-6xl">
    <Header />
    <div class="text-center">
      <img
        src="https://mdbcdn.b-cdn.net/img/new/avatars/8.webp"
        class="rounded-full w-32 mb-4 mx-auto"
        alt="Avatar"
      />
      <h5 class="text-xl font-medium leading-tight mb-2">John Doe</h5>
      <p class="text-gray-500">Web designer</p>
    </div>
    <Footer />
  </div>
);
ReactDOM.render(<App />, document.getElementById("app"));
```

```dev-server```를 재시작한다. 브라우저에서 아래의 그림을 볼 수 있다.  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/7de6abd4-64ff-401f-bec1-69f80d6bd697)  

위 코드와 UI를 통해서 두 개의 어플리케이션 사이에서 컴포넌트들이 (마이크로 프론트엔드를 적용해서) 성공적으로 공유되고 있음을 보았다.  

## 결론 
이번 글에서 우리는 마이크로 프론트엔드 개념을 예제와 함께 살펴보았으며, 프론트엔드 모노리스 어플리케이션 대비 장점을 나열하였다. 마이크로 프론트엔드를 적용함으로써 어떤 나이스한 기능을 쉽게 적용하여 제공할 수 있다.  

```create-mf-app```을 사용해서, 마이크로 프론트엔드 아키텍처를 쉽게 구현하였다. 개인적으로는 마이크로 프론트엔드 스타일을 좋아한다. 이는 팀들 사이에서 유지보수를 쉽게 해 주기 때문이다. 추가적으로 프론트엔드 빌딩과 보안도 꽤 우아하게 관리된다.  

모두에게 유익한 글이 되었으면 좋겠고 작은 질문이라도 (있으면) 남겨 주시기 바랍니다. Happy coding !!!
