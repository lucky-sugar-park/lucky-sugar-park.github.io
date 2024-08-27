---
title: "Micro Frontend using angularjs 1.x and reactjs with examples"  
last_modified_at: 2024-08-27T14:54:10-05:00
categories:
  - Blog
tags:
  - Microfrontend
  - React.js
  - Angularjs 1.x
  - Microfrontend integration bewteen reactjs and angularjs
---

> 불행하게도 2013년도 ~ 2017년도 사이에 Angularjs 1.x버전으로 개발된 SPA에 제법 있으며,  
> 시간이 지날 수록 개발자 수급이 어려워지며, 복잡해져서 변경 요구에 대응하기가 점점더 어려워지는 경향이 있다.  
> 이런 이유 때문에 유지보수가 용이한 React/Vue/Angular 기술 + Micro frontend 아키텍처로 전환하고자 하는 요구가 많이 있다  
> 하지만 이 작업은 매우 어려운 일이다. 특히 SPA의 규모가 커질 수록 전환작업은 극한으로 어려워질 수 있다.  
> 특히 Angularjs 1.x는 너무 오래된 기술이기 때문에 최신 기술인 webpack과 관련된 글을 찾기가 쉽지 않은 관계로  
> 전환작업은 더욱 더 어려워 진다.  
> 기존 Angularjs 1.x 코드를 버리고, 모두 신규 React 컴포넌트로 개발한 후에 Big bang 방식으로 일괄적용 할 수도 있지만  
> 규모가 크고 Lagacy frontend를 "계속 서비스 + 기능 유지보수"하면서 점증적인 전환을 해야 할 경우 (사실상 대부분의 경우가 여기에 해당될 수 있음)  
> 전환 작업은 시간도 오래 걸리고 매우 어려워질 수 있다.
>
> 이 글에서는 전환시에 고려해야 할 사항들을 짚어보고, 점증적 전환을 위한 절차와 예제를 통해서 두 개의 자바 스크립트 프레임워크간  
> 통합을 어떻게 하는지를 설명한다  

## 목차
1. 전환시 고려사항  
2. 아키텍처  
3. 전환절차  
4. Angularjs 1.x와 React간 Microfrontend 통합 예제

## 전환시 고려사항  

## 아키텍처 

## 전환절차  

## Angularjs 1.x와 React간 Microfrontend 통합   
전체 소스코드는 [여기](https://github.com/micro-frontend-pilot) 를 참조한다.   
모두 4개의 Repository가 있으며 (pokemon-poc는 무시해도 됨), 각 Repository에 대한 설명은 아래의 표와 같다.   
<table>
  <thead>
    <tr>
      <td>이름</td>
      <td>설명</td>
      <td>비고</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>angularjs-app</td>
      <td>
        - Angularjs 1.6.9로 개발된 프로젝트이다.<br/>
        - 기존의 Angularjs router에서 React로 만들어진 컴포넌트를 import 하고, 특정 url로 요청이 오면 React 컴포넌트로 routing 한다<br/>
        - React로 만들어진 Container에서 Angularjs 컴포넌트를 가져다 재사용할 수 있도록 Microfrontend를 expose 한다 
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
    <tr>
      <td>container</td>
      <td>
        - React로 만들어진 프로젝트이다 <br/>
        - React 또는 Nextjs, Angularjs 1.x로 expose된 Micro frontend들을 동적으로 재사용한다.  
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
    <tr>
      <td>common</td>
      <td>
        - Copyright, Header 등의 공통기능 재사용을 위해 Micro frontend로 expose 하기 위한 프로젝트이다 <br/>
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
    <tr>
      <td>biz-next</td>
      <td>
        - 도메인 업무에 대한 Micro Frontend를 제공하기 위한 프로젝트이다. <br/>
        - SSR 및 CSR용 Micro Frontend 적용 예시를 위해서 Nextjs로 만들어져 있다 <br/>
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
  </tbody>
</table>

