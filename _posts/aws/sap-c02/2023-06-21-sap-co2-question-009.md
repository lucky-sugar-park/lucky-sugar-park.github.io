---
title: "AWS-SAP-CO2-Question-009"
last_modified_at: 2023-06-21T07:44:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is hosting a critical application on a single Amazon EC2 instance.
The application uses an Amazon ElasticCache for Redis single node for an in-memory data store.
The application uses an Amazon RDS for MariaDB DB instance for a relational database.
For the application to function, each piece of the infrastructure must be healthy and must be in an active state.
A sloutions architect needs to improve the application's architecture so that the infrastructure can automatically revocer from failure with the least possible downtime.
Which combination of steps will meet these requirements? (choose three)  
```
<br/>
A. Use an Elastic Load Balancer to distribute traffic across multiple EC2 instances. Ensure that the EC2 instances are part of an Auto Scaling group that has a minimum capacity of two instances.  
<br/>
B. Use an Elastic Load Balancer to distribute traffic across multiple EC2 instances. Ensure that the EC2 instances are configured in unlimited mode.  
<br/>
C. Modify the DB instance to create a read replica in the same Availability Zone. Promote the read replica to be the primary DB instance in failure scenarios.  
<br/>
D. Modify the DB instance to create a Multi-AZ deployment that extends across two Availability Zones.  
<br/>
E. Create a replication group for the ElasticCache for Redis cluster. Configure the cluster to use an Auto Scaling group that has a minimum capacity of two instances  
<br/>
F. Create a replication group for the ElasticCache for Redis cluster. Elable Multi-AZ on the cluster  

<br/><br/>
<details>
  <summary>정답</summary>
  ADF<br/>
  [https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/AutoFailover.html](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/AutoFailover.html)  
  <br/>
  C는 문제에서 "can automatically recover from failure with the least possible downtime"를 만족하기 어려움  (
  동일한 Zone에 놓는 것보다 서로 다른 Zone에 놓는 것이 가용성 측면에서 더 뛰어남.    
</deatils>
