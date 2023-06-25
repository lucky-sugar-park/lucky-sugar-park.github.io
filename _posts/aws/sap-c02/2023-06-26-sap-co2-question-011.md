---
title: "AWS-SAP-CO2-Question-011"
last_modified_at: 2023-06-26T07:40:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company has many AWS accounts and uses AWS Organizations to manage all of them.
A solutions architect must implement a slution that the company can use to share a common network across multiple accounts.
The company's infrastructure team has a dedicated infrastructure account that has a VPC.
The infrastructure team must use this account to manage the network.
Individual accounts cannot have the ability to manage their own networks.
However, individual accounts must able to create AWS resources within subnets.
Which combination of actions should the solutions architect perform to meet these requirements? (Choose two.)  
```
<br/>
A. Create transit gateway in the infrastructure account.  
<br/>
B. Enable resource sharing from the AWS Organizations management account.  
<br/>
C. Create VPCs in each AWS account within the organization in AWS Organizations.  
   Configure the VPCs to share the same CIDR range and subnets as the VPC in the infrastructure account.  
   Peer the CPVs in each individual account with the VPC in the infrastructure account.  
<br/>   
D. Create a resource share in AWS Resource Access Manager in the infrastructure account. 
   Select the specific AWS Organizations OU that will use the shared network.  
   Select eacth subnet to associate with the resource share.  
<br/>
E. Create a resource share in AWS Resource Access Manager in the infrastructure account.  
   Select the specific AWS Organization OU that will use the shared network.  
   Select each prefix lilst to associate with the resource share.  
<br/>
<details>
  <summary>정답</summary>
  Site: AD, Community: BD (96%)<br/>
  <br/>
  B는 Organization에서 각 계정들에게 리소스를 공유하기 위해서 필요한 스텝임.  
  D는 Organization에서 Infrastructure account가 다른 계정들에게 특정 서브넷을 공유할 수 있도록 허용해 주기 위해서 필요한 스텝임.  
  A는 문제에서 멀티플 VPC를 언급하지 않았기 때문에 답이 될 수 없음 (Transit Gateway 불 필요)  
  E에서 prefix 리스트를 사용하면, VPC의 각 서브넷을 공유하는 대신에 (단순히) 라우팅 테이블을 이용할 수 있음  
  C는 그냥 불필요함 (뭐하러 VPC를 여러개 만드나? )
</deatils>   
