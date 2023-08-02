---
title: "Kebernetes에서 istio와 keycloak를 활용해서 통합인증관리 구축"
last_modified_at: 2023-08-03T07:36:02-05:00
categories:
  - Blog
tags:
  - istio
  - oauth2-proxy
  - keycloak
---

> Kubernetes 환경에서 Micro service 아키텍처를 적용하고 서비스 메쉬를 구축할 필요성이 발생했다.
> 많은 리서치와 고민 끝에 (직접 IDP 역할을 할 수도 있지만) 다양한 IDP와 손쉽게 연계할 수 있는 keycloak를 적용하기로 결정햐였다.
> (DEX도 고려하였으나, 오픈소스의 완성도나 기능 측면에서 Keycloak이 뛰어난 것으로 판단하였다)  
> 서비스 매쉬로 istio를 적용하는 것까지는 어렵지 않았는데,
> 서비스 매쉬의 중요한 기능 중의 하나인 oauth2와 SAML 방식의 인증을 모두 지원할 수 있는 시스템을 구축하는 것은 결코 쉽지 않았다.
> 무엇보다 사례를 찾기가 어려웠고, 설령 유사한 사례를 찾았어도 oauth2-proxy나 keycloak 버전이 달라서 에러가 발생하기도 하였다.
> istio나 keycloak, oauth2-proxy의 각각 단편적인 설명을 하는 글들은 많이 있었는데,
> 이 들을 엮어서 실제로 어플리케이션이 구동하도록 구성하는 글은 찾을 수 없었으며, 기록으로 남겨야 겠다는 생각이 들었다.

## 1. 개요 
구성하고자 하는 최종 시스템은 아래 그림과 같다.  
   
