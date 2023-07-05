---
title: "AWS-SAP-CO2-Question-017"
last_modified_at: 2023-07-06T07:32:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
  - Direct Connect Gateway
---

```
A company with global offices has a single 1 Gbps AWS Direct Connect connection to a single AWS Region.
The company's on-premises network uses the connection to communicate with the company's resources in the AWS Cloud.
The connection has a single private virtual interface that connects to a single VPC.
A solutions architect must implement a solution that adds a redundant Direct Connect connection in the same Region.
The solution also must provide connectivity to other Regions through the same pair of Direct Connect connections as the company expands into other Regions.
Which solution meets these requirements?
```
<br/>
A. Provision a Direct Connect gateway. Delete the existing private virtual interface from the existing connection.  
   Create the second Direct Connection connection. Create a new private virtual interface on each connection, and connect both private virtual interfaces to the Direct Connect gateway.  
   Connect the Direct Connect gateway to the single VPC.  
<br/>
B. Keep the existing private virtual interface. Create the second Direct Connect connection. Create a new private interface on the new connection, and connect the new private virtual interface to the single VPC.  
<br/>
C. Keep the existing private virtual interface. Create the second Direct Connect connection. Create a new public virtual interface on the new connection, and connect the new public virtual interface to the single VPC.  
<br/>
D. Provision a transit gateway. Delete the existing private virtual interface from the existing connection. Create the second Direct Connection connection.  
  Create a new private virtual interface on each connection, and connect both private virtual interfaces to the transit gateway.  
  Associate the transit gateway with the single VPC.  
<br/>

<details>
  <summary>정답</summary>
  A
  <br/>
  두 개의 Direct Connect를 Direct Connection Gateway에 연결하고 VPC를 Direct Connection Gateway에 연결함. 
  private virtual interface는 (기존 것을 제거후) 새로 생성해서 Direct Connection Gateway에 연결함  
  이렇게 하면, 동일한 페어의 Direct Connect connection을 다른 리전까지 확장할 수 있음  
  Direct Connect Gateway를 사용하면 여러 개의 VPC와 on-premises 네트워크를 연결할 수 있음 (다른 계정, 다른 리전에서)  
  It also provides automatic failover and routing capabilities
  D는 Direct Connection Gateway 대신에 Transit Gateway를 사용했는데, 이 문제의 해결책이 되지 못함 
  Transit Gateway는 여러 개의 VPC와 on-premise간 연결에 사용함.  
  Transit Gateway는 Fail-over와 라우팅 기능을 제공하지 못함 (이 시나리오에서는 요구하고 있음)
</deatils>
