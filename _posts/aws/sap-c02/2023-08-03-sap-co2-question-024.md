---
title: "AWS-SAP-CO2-Question-024"
last_modified_at: 2023-08-03T06:45:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is developing a new service that will be accessed using TCP on a static port.
A solutions architect must ensure that the service is hightly available, has redundancy across Avaliability Zones,
and is accessible using the DNS name my.service.com, which is publicly accessible.
The service must use fixed address assignments so other companies can add the addresses to their allow lists.
Assuming that resources are deployed in multiple Availability Zones in a single Region, which solution will meet these requirements?
```
<br/>
A. Create Amazon EC2 instances with an Elastic IP address for each instance. Create a Network Load Balancer (NLB) and expose the static TCP port.  
   Register EC2 instances with the NLB. Create a new name server record set named my.service.com, and assign the Elastic IP addresses of the EC2 instances to the record set.  
   Provide the Elastic IP addresses of the EC2 instances to the other companies to add to their allow lists.  
<br/>
B. Create an Amazon ECS cluster and a service definition for the application. Create and assign public IP addresses for the ECS cluster.  
   Create a Network Load Balancer (NLB) and expose the TCP port. Create a target group and assign the ECS cluster name to the NLCreate a new A record set named my.service.com, 
   and assign the public IP addresses of the ECS cluster to the record set. Provide the public IP addresses of the ECS cluster to the other companies to add to their allow lists.  
<br/>
C. Create Amazon EC2 instances for the service. Create one Elastic IP address for each Availability Zone. 
   Create a Network Load Balancer (NLB) and expose the assigned TCP port. Assign the Elastic IP addresses to the NLB for each Availability Zone.  
   Create a target group and register the EC2 instances with the NLB.
   Create a new A (alias) record set named my.service.com , and assign the NLB DNS name to the record set.  
<br/>
D. Create an Amazon ECS cluster and a service definition for the application. Create and assing public IP address for each host in the cluster.  
   Create an Application Load Balancer (ALB) and expose the static TCP port. Create a target group and assign the ECS service definition name to ALB.  
   Create a new CNAME record set and associate the public IP addresses to the record set.  
   Provide the Elastic IP addresses of the Amazon EC2 instances to the other companies to add to their allow lists.
<br/>

<details>
  <summary>정답</summary>
  site: C, community: C(100%)
  <br/>
  이런 경우 ALB는 적합하지 않음  
  각 AZ마다 Elastic IP를 할당함  
  Create Amazon EC2 instances for the service. Create one Elastic IP address for each Availability Zone  
</deatils>
