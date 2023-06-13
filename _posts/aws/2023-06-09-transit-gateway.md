---
title: "AWS Transit gateway"
last_modified_at: 2023-06-14T07:20:02-05:00
categories:
  - Blog
tags:
  - AWS Transit Gateway
  - VPC Peering
---

## Transit Gateway 란
Transit Gateway는 VPC Peering 처럼 서로 다른 CPV간에 통신이 가능하도록 하는 서비스이다.  
VPC Peering은 **1 대 1 VPC 연결만 지원**하여 직접적으로 연결되지 않은 VPC에 직접 접근할 수 없었다면  
Transit Gateway는 중앙 허브를 통해 **여려 VPC간 연결정책을 중앙에서 관리**할 수 있고,  
VPN을 통해서 VPC와 On Premise 네트워크를 연결할 수 있다.  

## Transit Gateway 특징
- 중앙허브와 VPN을 통해 VPC와 On Premise 네트워크를 연결할 수 있다.  
- 복잡한 Peering 관계를 제거하여 네트워크를 간소화 시킬 수 있다.  
- 클라우드 라우터 역할을 하므로 새로운 연결을 한 번만 추가하면 된다.  
- 서로 다른 리전의 Transit Gateway와 Peering 연결을 통해서 리전간 연결이 가능  

## Transit Gateway 필요성
VPC Peering을 사용해 같은 리전에 있는 VPC와 다른 리전에 있는 VPC를 연결하고 VPN을 통해 On Premise 네트워크와 연결하기 위해서는  
다음과 같은 구조로 인프라를 구성해야 한다  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/ff4d4400-178a-4a87-bd1a-5c36fa8a410c)

VPC Peering을 사용할 경우, 규모가 커질 수록 Peering 관계가 복잡해지고 인프라를 관리하기가 어려워진다.  
또한 On Premise 네트워크와 연결하기 위해서는 VGW (Virtual Private Gateway)를 각 리전별로 구성해야 하고  
VPC 한개를 추가하게 되면 수 많은 VPC Peering Connection을 추가해 줘야 한다.  

하지만 Transit Gateway를 사용하면 중앙 허브를 통해 위와 같은 인프라를 보다 간단하고 관기하기 쉬우면서도 확장성이 있는 구조로 변경할 수 있다.  
VPC를 Transit Gateway에 연결해주기만 하면 Transit Gateway에 연결된 다른 모든 VPC와의 통신이 가능하게 된다.  
또한 CGW (Customer Gateway)와 Transit Gateway를 연결하게 되면 Transit Gateway에 연결된 모든 VPC는 VPN을 통해서 연결이 가능하다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/e3ea25e0-0907-4607-aa06-33825ffac2bd)  

연결대상 VPC가 많으면 VPC Peering 방식으로는 복잡성이 증가하고 관리가 어려워지는데, 이 때에 Transit Gateway를 사용한다면 이런 문제를 해결할 수 있다  

## VPC Peering 대비 차이점   
**비용**
다만 같은 조건의 Transit Gateway방식은 VPC Peering 대비 약 1.5배 정도의 비용이 소요된다.  
<br/>
**VPC 연결개수**
VPC Peering 방식은 VPC당 125개까지 연결이 가능하고 Transit Gateway는 Gateway당 최대 5,000 개의 VPC를 연결할 수 있다.  
<br/>
**대역폭**
VPC Peering: 대역폭 제한이 없음  
Transit Gateway:  최대 대역폭이 50Gbps로 제한이 있음

<br/><br/>
** 아래 주소의 글을 참조 했습니다 **  
[https://yoo11052.tistory.com/170]https://yoo11052.tistory.com/170
