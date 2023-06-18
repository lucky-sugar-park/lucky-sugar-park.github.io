---
title: "AWS-SAP-CO2-Question-007"
last_modified_at: 2023-06-19T07:18:10-05:00
categories:
  - Blog
tags:
  - 
---

A Company is running a tranitional web application on Amazon EC2 instance.  
The company needs to refactor the application as microservices that run on containers.
Separate versions of the application exist in two distinct evcironments: production and testing.
Load for the application is variable, but the minumum load and the maximum load are known.
A solutions architect needs to design the updated application with a serverless architecture that mimimizes operational complexity.
Which solution will meet these requirements MOST cost-effectively?

<br/><br/>
A. Upload the conteiner images to AWS Lambda as functions. Configure a concurrency limit for the associated Lambda functions to handle the expected peak load. Configure two separate Lambda integration within Amazon API Gateway: one for production and one for testing.  
<br/>
B. Upload the container images to Amazon Elastic Container Resgistry (Amazon ECR). Configure two auto scaled Amazon Elastic Container Service (Amazon ECS) clusters with the Fargate launch type to handle the expected load. Deploy tasks from the ECR images. Configures two separate Application Load Balancers to direct traffic to the ECS clusters.  
<br/>
C. Upload the container images to Amazon Elastic Container Registry (Amazon ECR). Congiure two auto scaled Amazon Elastic Kubernetes Service (Amazon EKS) clusters with Fargate launch type to handle the expected load. Deploy tasks from the ECR images. Configure two separate Application Load Balancers to direct traffic to the EKS clusters.  
<br/>
D. Upload the container images to AWS Elastic Beanstalk. In Elastic Beanstalk, create separate environments and deployments for production and testing. Configure two separate Application Load Balancers to direct traffic to the Elastic Beanstalk deployments.

<br/><br/>
<details>
  <summary>정답</summary>
  B (79%)<br/>
  A, B, C가 모두 working 가능한 것인데, 비용 측면만 보면 A가 가장 저렴함 (비용측면: A < B < C)  
  하지만 문제 사이트 커뮤니티에서는 B를 정답으로 추천하는 사람이 가장 많음  
  -> A는 문제를 만족하기 힘들다는 의견도 있음 (Lambda function으로 어플리케이션을 통째로 구현하기에는 무리가 따를 수도)   
  -> ECS는 docker container 오케스트레이녓 서비스, EKS는 쿠버네트스 컨테이너 서비스    
  -> A is not correct, AWS documentation says you can package and deploy Lambda functions as container images.  
  -> D: AWS Elastic Beanstalk에 컨테이너 이미지를 올리는 것은 불가능 (A도 마찬가지)  
</deatils>
