---
title: "Question-001"
last_modified_at: 2023-06-13T08:19:02-05:00
categories:
  - Blog
tags:
  - Hybrid DNS
  - Transit Gateway
  - Route 53
---

```
A company needs to architect a hybrid DNS solution. This solution will use an Amazon Route 53 private hosted zone for the domain cloud.example.com for the resources stored with VPCs.  
The company has the following DNS resolution requirements:

- On-premise systems should be able to resolve and connect to cloud.example.com  
- All VPCs should be able to resolve cloud.example.com  
- There is already an AWS Direct Connect connection between the on-premises corporate network and AWS Transit Gateway.  

Which architecture should the company use to meet these requirements with the HIGHEST performance?  

```

A. Associate the private hosted zone to all the VPCs. Create a Route 53 inbound resolver in the shared services VPC. Attach all VPCs to the transit gateway and create following rules in the on-premise DNS server for cloud.example.com that point to the inbound resover.  
<br/>
B. Associate the private hosted zone to all the VPCs. Deploy an Amazon EC2 conditional forwarder in the shared services VPC. Attach all VPCs to the transit gateway and create forwarding rules in the on-premise DNS server for cloud.example.com that point to the conditional forwarder.  
<br/>
C. Associate the private hosted zone to the shared services VPCreate a Route 53 outbound resolver in the shared services VPAttach all VPCs to the transit gateway and create forwarding rules in the on-premises DNS server for cloud.example.com that point to the outbound resolver.  
<br/>
D. Associate the private hosted zone to the shared services VPC. Create a Route 53 inbound resolver in the shared services VPC. Attach the shared services VPC to the transit gateway and create forwarding rules in the on-premises DNS server for cloud.example.com that point to the inbound resolver.  
<br/>

<details>
  <summary>정답</summary>
  A<br/>
  Transit gateway를 통해서 On premise 네트워크와 VPC 네트워크들을 연결하는 것이 가장 효과적인 방법이고, On premise의 DNS Resover에서 Rout 53의 Inbound로 forwaring하도록 Rule을 설정함으로써 On premise내의 서버들이 VPC 내의 도메인에 접근할 수 있다.  
  <br/>
  B의 경우에는 가능한 방법일 수는 있지만, Amazon의 Route 53을 경유하지 않는다  
  <br/>
  C는 Route 53의 Inbound와 Outbound가 뒤바뀐 것 같음.
  <br/>
  D는 private hosted zone만 Shared service VPC에 연결하는 것이 잘못 되었음  
</deatils>
