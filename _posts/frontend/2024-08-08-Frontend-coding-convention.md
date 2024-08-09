---
title: "Coding conventions for micro frontend with reactjs and nextjs"  
last_modified_at: 2024-08-08T18:40:10-05:00
categories:
  - Blog
tags:
  - Coding convention
  - Reactjs
  - Micorfrontend
  - Nextjs
---

0. 현행 화면을 분석해서 => 화면 개수 파악, 공통 컴포넌트 파악, 마이크로 프론트엔드 묶음 파악
1. 프론트엔드 아키텍처 정의,  코딩 컨벤션 정의, CICD 환경 정의
2. 컨테이너 컴포넌트에서 다른 microfrontend 컴포넌트 / 서비스 모듈을 실시간으로 binding 하는 방식 => 아마도  
3. Frontend + Backend를 하나의 서비스 개발 팀으로 묶는 개념의 개발조직 필요  
4. 최종적으로 현행 개발자 대상의 코딩 컨벤션 교육
5. Migration 방안

---
업무별 frontend/backend별 stage/production별 빌드 파이프라인을 구성함  



---
1. directory structure  (assets, components, views ...)  
2. component, view, widget, page
3. container, views, card, 
4. configuration ...
5. I28N  
6. SSR, CSR ...
7. architecture
8. environment variables
9. naming rule (directory structure, file, variable, component, widget ... camel case, snake case, kabap case ...)
10. common component, utils functions, common widgets etc ...  
