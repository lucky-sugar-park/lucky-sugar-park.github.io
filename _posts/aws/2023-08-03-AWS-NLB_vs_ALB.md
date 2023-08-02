---
title: "AWS NLB와 ALB 차이점"
last_modified_at: 2023-08-04T07:11:10-05:00
categories:
  - Blog
tags:
  - NLB
  - ALB
---

LB 종류
- CLB: Classic Load Balancer 
  - L4, L7 모두 지원  
  - EC2-Classic 대상으로 지원 
  - TCP/HTTP/HTTPS 지원 
  - X-Forwarede 헤더 지원 

- ALB  
  - L7 로드밸런싱
  - HTTP/HTTPS 프로토콜의 헤더를 보고 적절한 패킷으로 전송
  - IP + 포트 + 패킷으로 스위칭 가능
  - IP주소가 변동되기 때문에 클라이언트에서 접근할 ELB의 DNS 이름을 이용해야 함

- NLB
  - L4 로드밸런싱
  - TCP/IP 프로토콜의 헤더를 보저 적절한 패킷으로 전송
  - IP + 포트로 스위칭
  - 할당한 Elastic IP를 static IP로 사용이 가능하며 DNS Name과 IP주소 모두 사용가능
  - SSL 불가능 (어플리케이션에서 따로 적용해 줘야 함)  

- L4 로드밸런싱이 더 빠름
- ALB에 고정 IP 적용
  ALB는 기본적으로 IP가 변경되기 때문에 고정 IP를 가질 수 있는 NLB를 앞에 둠으로써 적용 가능
  ![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/3fb28b91-aefe-4fb0-81c3-e536080b5d1e)
  ALB 앞에 NLB를 두지만 CloudWatch, Lambda 함수를 이용해서 ALB의 IP가 변경되는 시점마다 변경된 IP에 맞게 설정해 줘야 함
  https://aws.amazon.com/ko/blogs/korea/using-static-ip-addresses-for-application-load-balancers/

- ALB 타켓그룹 지정  
  - 인스턴스 연결이 로드밸런서에서 설정됨 (웹 서버 접근로그에는 LB의 IP 주소가 캡쳐됨)
  - 클라이언트 IP를 얻기 위해서는 X-Forwarded-For 헤더를 사용함  (자동 추가됨)
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/52004911-1924-4a32-a421-718e8feb6abe)

- NLB 타켓그룹 지정  
  - Instance ID로 지정한 Target Group의 경우 DSR(Direct Server Return) 방식으로 동작, Response 시에 EC2 Instance는 직접 Client에게 패킷 전달.
    서버에서 클라이언트 아이피 확인 가능  
  - IP로 지정한 Target Group의 경우, 기존 방식대로 Request/Response가 모두 LB를 경유, 아웃바운드 통신이 되지 않는 Private 구간에서도 NLB를 이용하여 서비스 가능
    서버에서 클라이언트 아이피 확인 가능   
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/f994c18c-75b8-4346-a745-156bfd3dfbc9)
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/f9e2a2e7-c70e-4141-b831-0f82ebd6b233)  

  - IP로 지정한 타


