---
title: "AWS-SAP-CO2-Question-015"
last_modified_at: 2023-07-03T07:55:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is using multiple AWS accounts. The DNS records are stored in a private hosted zone for Amazon Route 53 in Account A.
The company's applications and databases are running in Account B.
A solutions architect will deploy a two-tier application in a new VPC.
To somplify the configuration, the db.example.com CNAME record set for the Amazon RDS endpoint was created in private hosted zone for Amzaon Route 53.
During deployment, the application failed to start.
Troubleshooting reveled that db.example.com is not resolvable on the Amazon EC2 instance.
The solutions architect confirmed that the record set was created correctly in Route 53.
Which combination of steps should the solutions architect take to resolve this issue? (Choose two.) 
```
<br/>
A. Deploy the database on a separated EC2 instance in the new VPC. Create a record set for instance's private IP in the private hosted zone.  
<br/>
B. Use SSH to connect to the application tier EC2 instance. Add an RDS endpoint IP address to the /etc/resolv.conf file.  
<br/>
C. Create an authorization to the associate the private host zone in Account A with the new VPC in Account B.  
<br/>
D. Create a private hosted zone for the example com domain in Account B. Configure Route 53 replication between AWS accounts.
<br/>
E. Associate a new VPC in Account B with a hosted zone in Account A. Delete associate authorization in Account A.
<br/>

<details>
  <summary>정답</summary>
  Site: BC, Community: CE (100%)
  <br/>
  [private hosted zone 참조](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zone-private-associate-vpcs-different-accounts.html)  
  C번은 Account B에서의 VPC가 Account A의 private hosted zone에 DNS를 resolve하기 위해서 필요한 스텝임  
  E번은 Account B에서 Assocation이 이루어진 후에 Account A에서 권한을 제거해 줘야 함  
  B is incorrect because modifying the /etc/resolv.conf file on the EC2 instance would not resolve the issue since the issue is with the Route 53 configuration.
</deatils> 
