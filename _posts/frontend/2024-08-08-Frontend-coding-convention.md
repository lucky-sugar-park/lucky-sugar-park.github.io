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

### 업무진행 순서
1. 현행 화면을 분석해서 => 화면 개수 파악, 공통 컴포넌트 파악, 마이크로 프론트엔드 묶음 파악
2. 프론트엔드 아키텍처 정의,  코딩 컨벤션 정의, 프론트앤드 CICD 환경, 점증적 마이그레이션 방안 정의
3. 현행 개발자 대상 내용 설명 (아키텍처, 코딩컨벤션 등)  

### 고려사항
1. 컨테이너 컴포넌트에서 다른 microfrontend 컴포넌트 / 서비스 모듈을 실시간으로 binding 하는 방식 => 아마도  
2. Frontend + Backend를 하나의 서비스 개발 팀으로 묶는 개념의 개발조직 필요  

---
업무별 frontend/backend별 stage/production별 빌드 파이프라인을 구성함  

![image](https://github.com/user-attachments/assets/646f10e7-8871-4bf1-baa8-092bccf8699a)   

CICD 구성도  

![image](https://github.com/user-attachments/assets/757849f8-4597-4599-b993-9b5fb1ca003c)

개발조직 구성방안   

![image](https://github.com/user-attachments/assets/a42091a4-dcba-4c2e-bd83-dd3a4cffab59)


마이크로 프론트앤드 개념  

![image](https://github.com/user-attachments/assets/41f44cdf-b740-468d-8924-db32ad046294)  

![image](https://github.com/user-attachments/assets/93a1df38-00ed-4fe3-b588-d2e3622e4420)   

 ![image](https://github.com/user-attachments/assets/6f426034-5272-44a3-b70b-0b9143abfefa)   



### Coding Convention 
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
