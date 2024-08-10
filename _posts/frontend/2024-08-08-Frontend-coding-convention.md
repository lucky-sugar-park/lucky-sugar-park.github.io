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


---
### Coding Convention  (React 권장 코딩 컨벤션을 수용해야 함)
특정 조직 / 시스템에 맞는 코딩 컨벤션이 필요함. 코딩 컨벤션에는 다음과 같은 내용들이 포함되어 있어야 함    
1. directory structure  (assets, utils, components )
   - 디레토리 이름은 kebap case를 기준으로 함
   - 조직에 최적화된 (문화 등 고려) 디렉토리 정의가 중요함
2. 파일 이름  
   - Component 파일 / Page 파일 / View 파일 / widget 파일 ...  : 파스칼 케이스
   - function 파일 등 : uitls function 등, 케밥 케이스
3. 파일내 이름 들
   - 클래스 이름: 파스칼 케이스
   - 클래스 인스턴스 이름: 카멜 케이스  
   - 변수 이름: 카멜 케이스
   - 상수 이름: 스네이크 케이스  
4. 용어 표준화
   - component, view, widget, page, card, container 등의 용어 정의  
5. directory 내 index.js를 반드시 작성할 것 
6. configuration
   - 각종 설정 등을 어떻게 할 것인지에 대한 표준화 필요
   - 각종 환경 변수 ...
7. 다국어 지원   
8. SSR, CSR ...
9. SSE (서버로부터의 이벤트 처리 방안), 내부 메시지 통신 방안 (redux 적용)
