---
title: "AWS-SAP-CO2-Question-030"
last_modified_at: 2023-08-16T07:19:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company has 50 AWS accounts that are members of an organization in AWS Organizations.  
Each account contains multiple VPCs.  
The company wants to use AWS Transit Gateway to establish connectivity between the VPCs in each member account.  
Each time a new member account is created, the company wants to automate the process of creating a new VPC and a transit gateway attachment.  
Which combination of steps will meet these requirements? (Choose two.)
```
<br/>
A. From the management account, share the transit gateway with member accounts by using AWS Resource Access Manager.
<br/>
B. From the management account, share the transit gateway with member accounts by using an AWS Organizations SCP.
<br/>
C. Launch an AWS CloudFormation stack set from the management account that automatically creates a new VPC and a VPC transit gateway attachment in a member account. Associate the attachment with the transit gateway in the management account by using the transit gateway ID.
<br/>
D. Launch an AWS CloudFormation stack set from the management account that automatically creates a new VPC and a peering transit gateway attachment in a member account. Share the attachment with the transit gateway in the management account by using a transit gateway service-linked role.
<br/>
E. From the management account, share the transit gateway with member accounts by using AWS Service Catalog.
<br/>
<details>
  <summary>정답</summary>
  site: AC, community: AC(100%)
  <br/>
  CloudFormation: AWS 리소스를 모델링하고 설정하여 리소스 관리 시간을 줄이고 AWS에서 실행되는 애플리케이션에 더 많은 시간을 사용하도록 해 주는 서비스.  
  <br/>
  IaC와 같은 개념
  <br/>
  관리 계정에서 Transit gateway를 공유할 수 있도록 설정하고  
  CloudFormation을 통해서 계정이 생성되면 자동으로 새로운 VPC와 VPC가 Transit gateway에 붙도록 설정하면 됨
</deatils>
