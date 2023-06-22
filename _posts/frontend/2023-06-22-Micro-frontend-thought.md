---
title: "Method of micro frontend implementation"
last_modified_at: 2023-06-22T06:46:02-05:00
categories:
  - Blog
tags:
  - Micro frontend
---

[Martin Flower 님의 블로그](https://martinfowler.com/articles/micro-frontends.html) 번역을 베이스로 했습니다  
<br/>

> 좋은 프론트엔드 개발은 어렵다. 많은 팀이 크고 복잡한 제품을 동시에 작업할 수 있도록 프론트엔드 개발을 확장하는 것은 훨씬 더 어렵다.
> 여기에서는 모놀리스를 더 작고 관리가 용이한 조각으로 나누는 최근 추세와, 아 아키텍처가 어떻게 효율적으로 프론트엔드 코드 작업을 할 수 있는지와 그 효과가 무엇인지를 설명한다.
> 다양한 혜택 그리고 이를 이루기 위한 비용에 대해 이야기 하고, 사용 가능한 구현 옵션 중 일부를 다루고 예제를 통해서 자세히 살펴본다.

최근 많은 기업들이 복잡하고 현대적인 웹 개발에 필요한 아키텍처와 조직구조에 점점 더 많은 관심을 기술이고 있다.  
모노리스식 프론트엔드를 (독립적으로 개발, 테스트 및 배포할 수 있는) 더 작고 단순한 덩어리로 분해하는 패턴이 등장하는 동시에 고객에게는 여천히 응집력 있는 단일 제품으로 보이도록 하는 
마이크로 프론트엔드 기술이 많은 관심을 받고 있으며, 다음과 같이 정의한다.  
<br/>
> "독립적으로 제공 가능한 프론트엔드 어플리케이션이 더 큰 전체로 구성되는 아키텍처 스타일"  
<br/>

미국에 소재한 글로벌 소프트웨어 컨설팅 회사인) Thoughtworks (소트웍스)에서는 2016년부터 마이크로 프론트엔드를 조직에서 갖추어야 할 중요한 기술로 나열했습니다.  
사실 백엔드 보다는 프론트엔드 개발에 더 많은 개발자의 노력과 땀이 소요되기 때문에, 프론트엔드에서의 Business Agility를 갖출 수 있느냐의 여부가 조직의 경쟁력에 많은 영향을 미친다고 볼 수 있다.  
이런 측면에서 보면 소트웍스에서의 마이크로 프론트엔드 기술 선정은 (어찌보면) 당연하다고 볼 수도 있겠다.  

마이크로 프론트엔드를 적용하면 많은 이점을 얻을 수 있다. 하지만 단점도 존재할 수 있는데 (예를 들면, 코드가 중복될 수 있고 이로 인해서 사용자가 다운로드해야 하는 바이트수가 증가하거나 개발팀 자율성의 급격한 증가로 인해 팀이 작업하는 방식이 파편화 될 수 있음)  
그럼에도 불구하고 이런 단점을 얼마든지 극복할 수 잇으며, 단점보다는 얻을 수 있는 이점이 훨씬 더 크다고 믿고 있다.  

## 마이크로 프론트엔드 채택으로 얻을 수 있는 이익 
특정 기술 접근방식이나 구현 세부사항 측면에서 마이크로 프론트엔드를 정의하는 대신, 나타나는 속성과 그 속성이 제공하는 이점 측면에서 살펴본다.  
<br/>
#### 증분 업그레이드  
증분 업그레이드는 (많은 조직에서) 마이크로 프론트엔드의 여정의 시작점이 된다.  
기존의 모노리스식 프론트엔드는 복잡하고 비대하며 시간이 지나면서 점점 스파게티 코드가 되어 간다.  
어느 시점에서는 차라리 전체 코드를 재작성하는 것이 더 낫다고 느끼는 시점이 온다.  
하지만 전체를 한꺼번에 다시 재작성 하는 것은 매우 위험한 일이며, 이 위험을 피하기 위해 오래된 프론트엔드를 하나씩 바꾸어 나가는 방식을 선호한다.  
<br/>
이럴 때에 종종 마이크로 프론트엔드 아키텍처로 이어지게 된다.  
프론트엔드 아키텍처를 체책함에 따라, 여러 팀이 참여하게 되며 각 팀은 프론트엔드의 맡은 부분에 대해 각각 결정을 내리고 아키텍처, 종속성 및 사용자 경험을 점진적으로 업그레이드할 수 있는 더 많은 자유가 주어지게 된다. 메인 프레임워크의 주요 변경사항이 있는 경우, (모든 마이크로 프론트엔드를 한번에 중단하고 모든 것을 업그레이드해야 하는 대신) 필요할 때마다 업그레이드 할 수 있다. 새로운 기술이나 새로운 상호 작용방식을 실헙하고 싶다면 원하는 방식으로 그렇게 할 수 있다.  
<br/>
#### 간단하고 분리된 코드 베이스
(당연한 말이지만) 각 개별 마이크로 프론트엔드의 소스코드는 단일 모놀리스 프론트엔드의 소스코드보다 훨씬 작다.  
이러한 작은 코드베이스는 개발자가 작업하기 더 간단하고 쉽다. 특히 서로에 대해 알면 안되는 구성 요소간의 의도하지 않은 부적절한 결합으로 인해 발생하는 복잡성을 피할 수 있다.  
마이크로 프론트엔드 아키텍처를 적용한다면, 마이크로 프론트엔드간 데이터 공유 등의 문제가 발생할 수가 있는데,  
때문에 (마이크로 프론트엔드 아키텍처를 적용한다면) 데이터와 이벤트의 흐름 등에 더 많은 노력을 기울여야 한다.   
<br/>
#### 독립배포
마이크로서비스 아키텍처와 마찬가지고, 독립배포는 마이크로 프론트엔드 아키텍처의 핵심이다.  
이렇게 하면 지정된 배포범위가 줄어들고 결과적으로 관련 위험이 줄어든다. 프론트엔드 코드가 호스팅되는 방식이나 위치에 상관없이 각 마이크로 프론트엔드에는 자체적으로  
지속적 빌드 파이프라인이 있어야 하며, 이 파이프라인은 프로덕션 단계까지 코드를 빌드, 테스트 및 배포하게 된다.  
다른 코드 베이스나 파이프라인의 현재 상태와 상관 없이 각 마이크로 프론트엔드를 (배포준비가 되면 언제든지) 배포할 수 있어야 한다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/21f78f72-8d82-4ad2-912e-954496cf338a)  

#### 자율적인 팀  
코드베이스와 릴리스 주기를 모두 분리함으로써 얻을 수 있는 이점으로, (아이디어 구상에서 개발 및 배포에 이르기까지) 완전한 독립적인 팀을 꾸릴 수 있다.  
팀은 고객에게 가치를 제공하는데 필요한 모든 것을 완전히 소유할 수 있으므로, 빠르고 효과적으로 움직일 수 있다.  
이것이 제대로 작동하려면 팀은 **기술적 능력이 아닌 비즈니스 기능의 수직적 조각을 중심으로 구성** 되어야 한다.
이렇게 하는 쉬운 방법 중 하나는 최종 사용자가 볼 수 있는 것을 기반으로 프론트엔드를 분할사는 것이다.  
따라서 각 마이크로 프론트엔드는 어플리케이션의 단일 페이지를 캡슐화하고 단일 팀에서 종단간 소유된다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/051e91a7-f3ea-4627-8ce6-ee452ceeed41)  

#### 간단히 말해서
요컨데, **마이크로 프론트엔드는 크고 무거운 것을 더 작고 관리하기 쉬운 조각으로 나눈 다음 이들 사이의 종석성에 대해서 명시하는 것이이다.**   
기술선택, 코드베이스, 팀 및 릴리스 프로세스는 모두 과도한 조정 없이 서로 독립적으로 작동하고 발전할 수 있어야 한다  

## 예제
고객이 배달음식을 주문할 수 있는 웹사이트를 상상해 보자. 표면적으로는 매우 간단한 개념이지만, 잘 세부적으로 들여다보면 놀라운 양의 세부사항이 있다.  
<br/>
- 고객이 레스토랑을 둘러보고 검색할 수 있는 랜딩 페이지가 있어야 한다. 식당은 가격, 요리 또는 고객이 이전에 주문한 것을 포함한 여러 속성으로 검색 및 필터링 할 수 있어야 한다.
- 각 레스토랑에는 메뉴 항목을 표시하고 고객이 할인, 식사거래 및 특별 요청과 함께 먹고 싶은 것을 선택할 수 있는 자체 페이지가 필요하다.
- 고객은 주문 내역을 보고, 배송을 추적하고 결제 옵션을 사용자 지정할 수 있는 프로필 페이지가 있어야 한다.

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d44b4463-0fda-406a-9467-58e7fd1f4420)  

각 페이지에는 각기 전담 팀을 정당화할 수 있을 만큼 충분히 복잡하며 각 팀은 다은 모든 팀과 독립적으로 페이지에서 작업할 수 있어야 한다.  
다른 팀과의 충돌이나 조정에 대해 걱정하지 않고 코드를 개발, 테스트, 배포 및 유지할 수 있어야 한다. 그러나 고객은 여전히 하나의 완벽한 웹사이트를 보게 될 것이다. 

## 통합접근법 
위의 상당히 느슨한 정의를 감안할 때 마이크로 프론트엔드라고 합리적으로 불릴 수 있는 많은 접근이 있다. 여기에서는 몇 가지 예를 보여주고 장단점에 대해서 알아본다.  
모든 접근방식에 걸쳐 나타나는 아키텍처는 "일반적으로 어플리케이션의 각 페이지에 대한 마이크로 프론트엔드가 있고 다음과 같은 일을 하는 단일 어플리케이션이 있다" 이다.  
<br/>
- 머리글 및 바닥글과 같은 공통 페이지 요소를 렌더링 한다.  
- 인증 및 탐색과 같은 Cross cutting과 같은 문제가 해결되어야 한다.
- 다양한 마이크로 프론트엔드를 페이지와 함께 가져오고 각 마이크로 프론트엔드에게 언제, 어디서 렌더링할지를 알려준다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/f0814192-2184-4a21-af92-394580ab448e)   
<일반적으로 페이지의 시각적 구조에서 아키텍처를 파생할 수 있다>  

#### Server side template composition  
각각 다른 앱에 맞는 HTML을 서비스 하는 서버를 backend에 두고, 요청한 URL에 따라 HTML을 writing 해주는 서버를 그 앞단에 놓아서 요청에 적합한  HTML을 응답으로 보내주는 구조이다.  
```
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>Feed me</title>
  </head>
  <body>
    <h1>🍽 Feed me</h1>
    <!--# include file="$PAGE.html" -->
  </body>
</html>
```
위 코드는 공통된 부분을 포함하고 있는 index.html 코드이다. 이 코드에는 서버 측의 Plug-in 페이지를 포함하고 있다.  
이제, Nginx 서버를 사용한다고 가정하자. $PAGE라는 변수를 요청 URL에 따라서 다르게 매칭시킬 수 있다.  
```
server {
    listen 8080;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;
    ssi on;

    # Redirect / to /browse
    rewrite ^/$ http://localhost:8080/browse redirect;

    # Decide which HTML fragment to insert based on the URL
    location /browse {
      set $PAGE 'browse';
    }
    location /order {
      set $PAGE 'order';
    }
    location /profile {
      set $PAGE 'profile'
    }

    # All locations should render through index.html
    error_page 404 /index.html;
}
```

위 방법은 상당피 일반적인 서버측 구성이다. 우리가 위 예시를 마이크로 프론트엔드라고 정당하게 부를 수 있는 이유는 각 조각이 독립적인 팀에서 제공할 수 있는 독립적인 도메인 개념을 나타내는 방식으로 도메인을 분할했기 때문이다.  
위 코드는 단순한 것이지만, 각각 고유한 배포 파이프라인이 있다고 가정하여 다른 페이지에 영향을 미치지 않는 방향으로 어떤 페이지에 변경사항을 배포할 수 있다.  

더 큰 독립성을 위해 각 마이크로 프론트엔드를 렌더링/제공하는 별도의 서버를 둘 수도 있으며 이 서버는 다른 서버들의 앞 단에 위치할 수 있다.  
응답을 캐싱한다면 대기시간에 영향을 주지않고 서비스를 제공할 수 있을 것이다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d8fafe0a-09a3-4511-a387-5d838e8e93be)   

위 예제는 마이크로 프론트엔드가 반드시 새로운 기술일필요도 없고, 복잡할 필요도 없음을 잘 보여준다. 어떤 설계 결정이 코드베이스와 팀의 자율성에 어떤 영향을 미치는지 주의를 기술이는 한, 기술 스택에 관계 없이 많은 동일한 이점을 얻을 수 있다.  

최근의 웹 개발 기술과 매핑시켜보면, 특정 페이지 단위의 앱을 도메인별로 여러 개의 Nextjs 앱 서버에 배포해 놓고,
앞 단에 basePath로 요청이 들어왔을 때에, 뒷 단의 Nextjs로 필요한 페이지를 요청하고 앞 단의 서버에서 이를 통합하는 방식을 생각해 볼 수 있다.  

이 방법과 관련있는 솔루션으로 Nextjs의 multi-zones가 있다고 한다. Nextjs로 개발된 여러 개의 웹앱을 묶어서 배포하기 용이하게 만드는 방법이다.  
vercel은 multi-zones를 마이크로 프론트엔드 아키텍처를 구현하는 방법으로 제시 (https://vercel.com/templates/next.js/microfrontends)하고 있지만,
웹앱별 배포로 해결할 수 없는 문제는 해결하기 어려이 있다고 한다.  (https://maxkim-j.github.io/posts/runtime-integration-micro-frontends/) 참조.  

#### Build time integration  
각 마이크로 프론트엔드를 패키지 저장소에 (예, npm repository) 배포하고, 빌드시에 어플리케이션에 라이브러리 종속으로 포함되도록 하는 방식이다.  
쉽게 말하면, UI 부분들을 npm 패키지로 만들어 리포지토리에 배포하고 어플리케이션의 package.json에 의존성과 버전을 표기한 후에 빌드/배포 하는 방식을 의미한다.  
아래의 package.json 파일 일부 내용을 보자.  
```
{
  "name": "@feed-me/container",
  "version": "1.0.0",
  "description": "A food delivery web app",
  "dependencies": {
    "@feed-me/browse-restaurants": "^1.2.3",
    "@feed-me/order-food": "^4.5.6",
    "@feed-me/user-profile": "^7.8.9"
  }
}
```

이 접근 방식은 javascript 번들을 생성하여 다양한 어플리케이션에서 공통적으로 사용되는 코드의 중복을 제거할 수 있으나,   
마이크로 프론트엔드로 배포된 일부 패키지가 변경되는 경우에, 모든 단일 마이크로 프론트엔드를 다시 컴파일하고 릴리스해야 함을 의미한다.  

때문에 이 방식은 어플리케이션을 독립적으로 개발/테스트할 수 있는 별도의 코드베이스로 나누는 효과는 볼 수 있으나,  
모노리스 프론트엔드로 인한 고통을 여전히 해결하지 못한다 (웹 앱별 배포가 불가능함으로 인한, 유지보수의 어려움)  

#### Run time integration via iframe  
여기서부터 확실히 페이지 단위 이하로 배포 단위를 분리할 수 있는 방법들이면서, 분절된 UI 컴포넌트를 런타임에 통합하는 방식이다.  

iframe은 현재도 배포단위를 UI 단위로 분리하기 위해, 혹은 다은 웹앱의 UI 일부를 다른 웹 앱안으로 통합하기 위해 종종 사용되는 방식으로써,  
iframe을 사용하면 특정 외부 URL로 배포해 놓은 UI의 일부를 HTML로 통째로 기존 HTML에 쉽게 놓을 수 있고,  
독립적인 하위 페이지로써 페이지를 쉽게 만들고 배포할 수 있다.  
또한 서로 간섭하지 않는 스타일 및 전역변수 측면에서 상당한 수준의 격리를 이룰 수 있다.  
```
<html>
  <head>
    <title>Feed me!</title>
  </head>
  <body>
    <h1>Welcome to Feed me!</h1>

    <iframe id="micro-frontend-container"></iframe>

    <script type="text/javascript">
      const microFrontendsByRoute = {
        '/': 'https://browse.example.com/index.html',
        '/order-food': 'https://order.example.com/index.html',
        '/user-profile': 'https://profile.example.com/index.html',
      };

      const iframe = document.getElementById('micro-frontend-container');
      iframe.src = microFrontendsByRoute[window.location.pathname];
    </script>
  </body>
</html>
```

iframe은 새로운 기술이 아니며 그리 흥미롭지 않을 수도 있는데, 앞서 나열된 마이크로 프론트엔드의 주요 이점을 다시 살펴보면  
어플리케이션을 분할하고 팀을 구성하는 방법에 주의를 기울이면 iframe이 대부분 적합할 수 있다.  

하지만 iframe injection 같은 보안 문제 때문에 사용부담이 있을 수 있다.   

만약 iframe으로 분리되어 있는 UI 컴포넌트에서 다른 컴포넌트와 상태 값을 공유해야 한다면, window.postMessage(), EventListener와 같은  Web API를 활용하는 등 상태공유 방법을 고민해야 한다.  
<br/>

#### Run time integration via web component
웹 컴포넌트란 **모든 주요 브라우저에서 지원하는 웹 표준 기반의 재사용 가능한 클라이언트 사이드 컴포넌트** 이다. 코드에서 원하는 부분을 **캡슐화**하는 훌륭한 방법을 제공하며 모든 웹 어플리케이션과 웹 제이지에서 재사용할 수 있다.  
웹 컴포넌트에 대한 자세한 설명은 [여기](https://yozm.wishket.com/magazine/detail/1193/)를 참조한다.  

이 방식을 사용하면 iframe와 같은 보안 문제를 해결할 수 있으며 분리된 HTML을 기존 앱에 쉽게 통합할 수 있다.  
이 방식은 기존 React나 Vue등과 방식이 조금 달라져야 한다. 이 방식을 사용한다고 해서 기존 React와 같은 프레임워크 (또는 라이브러리)를 사용할 수 없는 것은 아니고,  
두 개의 기술 (Web Component + React, Web Component + Vue ...)을 조합해서 컴포넌트 개발하는 것도 가능하며, "Runtime integration via java script" 방식을 적용할 수 있다.  
> 사실 Martin Flowler가 왜 이 두개를 별개의 방식으로 구분했는지는 잘 이해가 가지 않는다. 결국 컴포넌트를 만드는 기술의 차이만 존재할 뿐, 마이크로 프론트엔드 관점에서 보면 동일하게 Webpack 5의 Module Fedration 기술을 사용할 수 있기 때문이다.
> 다만, 순수하게 Web Compoenent 기술만 사용한다면 컴포넌트간 커뮤니케이션 및 상태 데이터 등의 공유가 쉽지 않은 것처럼 보인다.
> 때문에 React + Web Component나 Vue + Web Component와 같은 조합으로 이런 문제들을 어느정도 해결할 수 있을 것 같다.  

<br/>

#### Run time integration via javascript 
가장 유연한 방식이며, 프론트엔드 개발 팀에서 가장 많이 채택되는 방식이다.  
이 방식은 UI 컴포넌트를 자바 스크립트 번들 단위로 배포단위를 나누고, 필요할 때에 번들을 로딩하여 런타임 시에 통합하는 방식이다.  
각 마이크로 프론트엔드는 태그를 사용하여 페이지에 포함되며,  
<scropt> 로드시 전역함수를 진입점으로 노출한다. 그런 다음 컨테이너 어플리케이션은 어떤 마이크로 프론트엔드를 마운트해야 하는지를 결정하고  
관련함수를 호출하여 마이크로 프론트엔드에게 언제, 어디서 렌더링되어야 하는지 알려준다.  

```
<html>
  <head>
    <title>Feed me!</title>
  </head>
  <body>
    <h1>Welcome to Feed me!</h1>

    <!-- These scripts don't render anything immediately -->
    <!-- Instead they attach entry-point functions to `window` -->
    <script src="https://browse.example.com/bundle.js"></script>
    <script src="https://order.example.com/bundle.js"></script>
    <script src="https://profile.example.com/bundle.js"></script>

    <div id="micro-frontend-root"></div>

    <script type="text/javascript">
      // These global functions are attached to window by the above scripts
      const microFrontendsByRoute = {
        '/': window.renderBrowseRestaurants,
        '/order-food': window.renderOrderFood,
        '/user-profile': window.renderUserProfile,
      };
      const renderFunction = microFrontendsByRoute[window.location.pathname];

      // Having determined the entry-point function, we now call it,
      // giving it the ID of the element where it should render itself
      renderFunction('micro-frontend-root');
    </script>
  </body>
</html>
```

위의 예와 같이 (빌드통합과 달리) 각각 bundle.js 파일을 독립적으로 배포할 수 있다.

React를 사용한다고 가정하면, "정의한 컴포넌트들을 떼어 내서 별도의 배포단위로 만들고 런타임 시에 합치는 것이 가능하다".  
작은 단위의 컴포넌트부터 런타임 시에 사용될 수 있는 독립된 번들로 만드는 것이다.  

React에서 제공하는 API들을 통해 어플리케이션 안에서 자연스럽게 배포단위별로 공유되는 상태관리가 가능하다. 또한 미리 로딩된 (어플리케이션이 가져온) CSS와 같은 Resource들도 곧바로 사용할 수 있다.  

이 방법의 가장 큰 장점은 배포단위를 유연하게 가져갈 수 있다는 것이다. 배포단위간 상태공유 작업이 다른 런타임 통합방식보다 이질적이지 않고,   
(모두 웹 어플리케이션 내부에서 가능하므로) 기존 배포단위를 합치거나 나누는 것이 비교적 간단함  

Webpack 5에서 제공되는 Module Fedration Plugin 이 이 방식에 해당되는 솔루션이다. 이 플러그인은 (분리된 배포단위의) 웹 어플리케이션이 모이는 일명 host와  
각각 분리된 배포단위인 remote 앱을 정의하고 런타임 시에 통합할 수 있는 옵션을 제공한다.  

이 플러그인 창시자인 Zack Jackson의 표현인데, 플러그인의 컨셉을 아주 잘 설명하고 있음  
> Module Federation Plugin을 사용하면 거대한 웹 앱을 따로 개발해 빌드/배포하는 것으로 MSA의 장점을 가져가고,
> 런 타임에서는 모놀리스처럼 통합되어 통합의 자연스러움과 상태관리의 이점을 얻을 수 있다  


## 스타일링 
언어로써의 CSS는 전통적으로 모듈 시스템, 네임스페이스 또는 캡슐화 없이 본질적으로 전역적이고 상속되며 연속적이다. 이러한 기능중 일부는 현재 존재하지만 브러우저 지원이 부족한 경우가 많다.  
마이크로 프론트엔드 환경에서는 이러한 문제중 많은 부분이 더 안 좋아 진다. 예를 들면, 어느 팀의 마이크로 프론트엔드에 ```h2 { color : black; }``` 이라는 스타일이 있고, 다른 팀에는 ```h2 { color: blue; }``` 이라는 스타일이 있는데, 동일한 페이지에 이 두개의 스타일이 있게 된다면 어느 한쪽은 잘못된 동작을 할 것이다.  이게 새로운 문제는 아니지만 서로 다른 팀에 의해서 각자의 스타일이 작성되고 (아마도) 별도의 저장소에 분할되어 발견하기가 더 어려울 것이다.  

수년에 걸쳐서 CSS를 보다 쉽게 관리할 수 있도록 많은 접근방식이 개발되어왔다. 일부는 [BEM](https://getbem.com/)과 같은 엄격한 명명규칙을 사용해서 선택자가 의도한 곳에서만 적용되도록 한다. 반면에 개발자 원칙에만 의존하는 것을 좋아하지 않는 사람들은 중첩된 스타일 같은 경우 네임스페이스 형식으로 사용할 수 있는 SASS와 같은 방식을 사용한다.  

새로운 접근 방식은 CSS 모듈 또는 다양한 [CSS-in-JS](https://mxstbr.com/thoughts/css-in-js/) 라이브러리 중 하나를 사용하여 프로그래밍 방식으로 모든 스타일을 적용하는 것이다. 이렇게 함으로써 개발자가 의도한 위치에서만 스타일이 직접 적용될 수 있다. 또한 [Shaow DOM](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_shadow_DOM) 은 (보다 플랫폼 기반의 접근하는 방식)을 지원하기 위해 스타일 격리도 제공한다.  

개발자로서 서로 독립적으로 스타일을 작성할 수 있는 방법을 찾고 코드가 단일 응용 프로그램으로 함께 구성될 때, 예측 가능하게 동작할 것이라는 확신을 갖는 한 선택하는 접근 방식은 그다지 중용하지 않다고 할 수 있다.  

## 공유 구성 요소 라이브러이  

## 어플리케이션간 통신  

## 백엔드 통신  

## 테스트 

## 자세한 예제 
자바 스크립트를 사용해서 컨테이너 어플리케이션과 마이크로 프론트엔드가 함께 통합되는 방법에 초점을 맞춘 예제를 보여준다. 최종결과는 [여기](https://demo.microfrontends.com) 에서 라이브로 볼 수 있으며, 전체 [소스코드](https://github.com/micro-frontends-demo)를 참조한다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/5ecd3960-e39e-49ea-aff7-1acf14bf8a23)  

데모는 모두 React.js를 사용해서 구축되었다. 하지만 **React가 아니더라도 이 아키텍처를 적용할 수 있다**는 점을 강조한다.  마이크로 프론트앤드는 다양한 도구 또는 프레임워크로 구현될 수 있다. React를 선택한 이유는 단지 친숙하기 때문이다.  

#### 컨테이너 
사용자의 진입점인 컨테이너부터 시작한다. 아래의 package.json 파일을 보자.  
```
{
  "name": "@micro-frontends-demo/container",
  "description": "Entry point and container for a micro frontends demo",
  "scripts": {
    "start": "PORT=3000 react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test"
  },
  "dependencies": {
    "react": "^16.4.0",
    "react-dom": "^16.4.0",
    "react-router-dom": "^4.2.2",
    "react-scripts": "^2.1.8"
  },
  "devDependencies": {
    "enzyme": "^3.3.0",
    "enzyme-adapter-react-16": "^1.1.1",
    "jest-enzyme": "^6.0.2",
    "react-app-rewire-micro-frontends": "^0.0.1",
    "react-app-rewired": "^2.1.1"
  },
  "config-overrides-path": "node_modules/react-app-rewire-micro-frontends"
}
```

React 및 
