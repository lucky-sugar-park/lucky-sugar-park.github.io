---
title: "Nextjs와 webpack5에서의 마이크로 프론트엔드"
last_modified_at: 2023-06-28T15:05:10-05:00
categories:
  - Blog
tags:
  - Next.js
  - Webpack5
  - Microfrontend
---
[여기](https://thekevinwang.com/2021/03/26/micro-frontends-nextjs)를 참조하였다

> Webpack 5의 Module Federation을 사용하면 마이크로 프런트엔드를 간단하게 만들 수 있다.
> 먼저 외부 마이크로 프런트엔드를 빌드하고 선택한 대상에 "Fedration Module"로 배포한다.
> 그런 다음 NextJS 애플리케이션에서 일부 구성을 업데이트하면 Webpack이 나머지 작업을 수행한다.
> 이것은 일종의 마술이다.
<br/>
2020년도에 Webpack 5가 출시되었고 Fedration Module 기능에 의해 (마이크로 프론트엔드 패러다임의) 새로운 가능성의 문을 열었다.

## 마이크로 프론트엔드

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/25c40314-29bc-4a05-a1fc-3527f3a07a67)  

위 그림에서 몇 개의 마이크로 프론트엔드를 볼 수 있다.  
각각의 마이크로 프론트엔드에 대해서 설명하는 대신에 NextJS 어플리케이션에 마이크로 프론트엔드를 추가하는 방법을 중심으로 설명하겠다.

## NextJS 앱의 마이크로 프론트엔드
마이크로 프론트엔드
