---
title: "AWS PrivateLink를 통해서 SaaS 서비스에 접근하기"
last_modified_at: 2023-06-28T07:46:10-05:00
categories:
  - Blog
tags:
  - PrivateLink
---

[원문](https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-access-saas.html)을 참조하실 수 있습니다

> AWS PrivateLink를 이용하면 마치 당신의 VPC 안에서 구동되는 것처럼 SaaS 어플리케이션에 private하게 연결할 수 있다.

## 개요 
AWS 마켓을 통해 또는 AWS 파트너에서 제공하는 (AWS PrivateLink를 통해 접근할 수 있는) SaaS 서비스을 제공 받을 수 있다.   

아래의 다이어그램은 VPC 엔트포인트를 사용하여 SaaS 제품에 연결하는 방법을 보여준다.  
서비스 공급자는 엔드포인트 서비스를 생성하고 고객에게 엔드포인트 서비스에 대한 접근 권한을 부여한다.  
서비스 소비자는 VPC에 있는 하나 이상의 서브넷과 엔드포인트 서비스 간에 연결을 설정하는 인터페이스 VPC 엔드포인트를 생성한다.


![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/2dccaef8-15bb-45d6-bc5f-6ef22a9946f5)  
