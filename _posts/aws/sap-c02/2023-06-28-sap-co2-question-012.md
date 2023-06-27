---
title: "AWS-SAP-CO2-Question-012"
last_modified_at: 2023-06-28T07:28:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
  - PrivateLink
---

```
A company wants to use a third-party software-as-a-service (SaaS) application.
The third-party SaaS application is consumed through several API calls.
The third-party SaaS application also runs on AWS inside a VPC.

The comapny will consume the third-party SaaS application from inside a VPC.
The company has internal security policies that mandate the use of private connectivity that does not traverse the internet.
No resources that run in the company VPC are allowed to be accessed from outside the company's VPC.
All permisstions must conform to the principles of least privilege.

Which solution meets these requirement? 
```
<br/>
A. Create an AWS PrivateLink internet VPC endpoint. Connect this endpoint to the endpoint service that the third-party SaaS application provides. Create a security group to limit the access to the endpoint. Associate the security group with the endpoint.  
<br/>
B. Create an AWS Site-to-Site VPC connection between the third-party SaaS application and the company VPC. Configure network ACLs to limit access across the VPN tunnels.  
<br/>
C. Create VPC peering connection between the third-party SaaS application and the company VPUpdateroute tables by adding the needed routes for the peering connection. 
<br/>
D. Create an AWS PrivateLink endpoint service. Ask the thrid-party SaaS provider to create an interface VPC endpoint for this endpoint service. Grant permissions for the endpoint service to the specific account of the third-party SaaS provider.
<br/>

<details>
  <summary>정답</summary>
  A
  <br/>
  [PrivateLine 설명참조](https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-access-saas.html)  
  PrivateLink는 기업의 VPC와 third-party SaaS application VPC간 secure and private 연결을 생성해 줌 (인터넷을 통하지 않음)  
  시큐리티 그룹을 이용하고 서비스의 엔드포인트 접근을 제한하면 최소한의 원칙을 지킬 수 있음
</deatils> 
