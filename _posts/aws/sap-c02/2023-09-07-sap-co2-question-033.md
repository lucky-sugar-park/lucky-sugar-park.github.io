---
title: "AWS-SAP-CO2-Question-033"
last_modified_at: 2023-09-07T07:17:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

A company is hosting a monolithic REST-based API for a mobile app on five Amazon EC2 instances in public subnets of a VPC.    
Mobile clients connect to the API by using a domain name that is hosted on Amazon Route 53.   
The company has created a Route 53 multivalue answer routing policy with the IP addresses of all the EC2 instances.   
Recently, the app has been overwhelmed by large and sudden increases to traffic.   
The app has not been able to keep up with the traffic.   
A solutions architect needs to implement a solution so that the app can handle the new and varying load.   
Which solution will meet these requirements with the LEAST operational overhead?   

<br/>

A. Separate the API into individual AWS Lambda functions.    
   Configure an Amazon API Gateway REST API with Lambda integration for the backend.   
   Update the Route 53 record to point to the API Gateway API.
<br/>
B. Containerize the API logic.   
   Create an Amazon Elastic Kubernetes Service (Amazon EKS) cluster.    
   Run the containers in the cluster by using Amazon EC2.   
   Create a Kubernetes ingress. Update the Route 53 record to point to the Kubernetes ingress.   
<br/>
C. Create an Auto Scaling group.   
   Place all the EC2 instances in the Auto Scaling group.   
   Configure the Auto Scaling group to perform scaling actions that are based on CPU utilization.   
   Create an AWS Lambda function that reacts to Auto Scaling group changes and updates the Route 53 record.   
<br/>
D. Create an Application Load Balancer (ALB) in front of the API.   
   Move the EC2 instances to private subnets in the VPC.   
   Add the EC2 instances as targets for the ALB.   
   Update the Route 53 record to point to the ALB.  
<br/>
<details>
  <summary>정답</summary>
  site: D, community: A(57%),C(24%),D(19%)
  <br/>
  Route 53과 ALB와는 적합하지 않음
  Serverless 방식이 가장 운영 오버헤드가 적을 수도 있음, 람다는 설정 범위 내에서 Auto scaling 할 수 있음 (A)
  => 하지만 API가 100개면 어떻게 할 것인가? 하나하나 람다로 옮길 것인가? 비즈니스 로직이 변경되면 어떻게 할 것인가? (C)  
     => 문제 지문에서 a monolith REST API라고 했음
</deatils>
