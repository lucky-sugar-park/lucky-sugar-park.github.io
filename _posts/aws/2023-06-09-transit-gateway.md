---
title: "AWS Transit gateway"
last_modified_at: 2023-06-09T14:36:02-05:00
categories:
  - Blog
tags:
  - Post Formats
  - readability
  - standard
---

## Transit Gateway
Transit Gateway는 VPC Peering 처럼 서로 다른 CPV간에 통신이 가능하도록 하는 서비스이다.  
VPC Peering은 **1 대 1 VPC 연결만 지원**하여 직접적으로 연결되지 않은 VPC에 직접 접근할 수 없었다면  
Transit Gateway는 중앙 허브를 통해 **여려 VPC간 연결정책을 중앙에서 관리**할 수 있고,  
VPN을 통해서 VPC와 On Premise 네트워크를 연결할 수 있다.  

## Transit Gateway 특징
- 중앙허브와 VPN을 통해 VPC와 On Premise 네트워크를 연결할 수 있다.  
- 복잡한 Peering 관계를 제거하여 네트워크를 간소화 시킬 수 있다.  
- 클라우드 라우터 역할을 하므로 새로운 연결을 한 번만 추가하면 된다.  
- 서로 다른 리전의 Transit Gateway와 Peering 연결을 통해서 리전간 연결이 가능  

## Transit Gateway 사용이유
VPC Peering을 사용해 같은 리전에 있는 VPC와 다른 리전에 있는 VPC를 연결하고 VPN을 통해 On Premise 네트워크와 연결하기 위해서는  
다음과 같은 구조로 인프라를 구성해야 한다  
<<그림>> http://yoo11052.tistory.com/170  
VPC Peering을 사용할 경우, 규모가 커질 수록 Peering 관계가 복잡해지고 인프라를 관리하기가 어려워진다.  
또한 On Premise 네트워크와 연결하기 위해서는 VGW (Virtual Private Gateway)를 각 리전별로 구성해야 하고  
VPC 한개를 추가하게 되면 수 많은 VPC Peering Connection을 추가해 줘야 한다.  

하지만 Transit Gateway를 사용하면 중앙 허브를 통해 위와 같은 인프라를 보다 간단하고 관기하기 쉬우면서도 확장성이 있는 구조로 변경할 수 있다.  
VPC를 Transit Gateway에 연결해주기만 하면 Transit Gateway에 연결된 다른 모든 VPC와의 통신이 가능하게 된다.  
또한 CGW (Customer Gateway)와 Transit Gateway를 연결하게 되면 Transit Gateway에 연결된 모든 VPC는 VPN을 통해서 연결이 가능하다.  
