---
title: "AWS-SAP-CO2-Question-018"
last_modified_at: 2023-07-07T16:51:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company has a web application that allows users to upload short videos.
The videos are stored on Amazon EBS volumes and anlyzed by custom recognition software for categorization.

The website contains static contents that has variable traffic with peak in cretain months.
The architecture consists of Amazon EC2 instances running in an Auto Scaling group for the web application and
EC2 instances running in an Auto Scaling group to process an Amazon SQS queue.
The company wants to re-architect the application to reduce operational overhead using AWS managed services where possible and remove dependencies on third-party software.

Which solution meets these requirement? 
```
<br/>
A. Use Amazon ECS containers for the web application and Spot instances for the Auto Scaling group that processes the SQS queue. Replace the custom software with Amazon Rekognition to categorize the videos.  
<br/>
B. Store the uploaded videos in Amazon EFS and mount the file system to the EC2 instances for the web application. Process the SQS queue with an AWS Lambda function that calls the Amazon Rekoginition API to categorize the videos.  
<br/>
C. Host the web application in Amazon S3. Store the uploaded videos in Amazon S3. Use S3 event notification to publish events to the SQS queue. Process the SQS queue with an AWS Lambda function that calls the Amazon Rekognition API to categorize the videos.  
<br/>
D. Use AWS Elastic Beanstalk to launch EC2 instances in an Auto scaling group for the web application and launch a workder environment to process the SQS queue. Replace the custom software with Amazon Rekognition to categorize the videos.  
<br/>

<details>
  <summary>정답</summary>
  Site: D(13%), Community: C(87%)
  <br/>
  Operational overhaed를 줄이라고 했으므로 S3를 활용하는 것이 맞음 (managed service를 사용하라고 했음)  
  S3에서 호스팅하는 웹 서버는 상당히 고가용성과 확장성을 제공하며 변동이 심한 트래픽에 효율적으로 대응할 수 있음.  
  S3에 업로드하는 비디오는 이벤트에 의해서 처리될 수 있음 (람다에 의해서 트리거링 될 수 있음) 람다가 Rekognition API를 호출할 수 있음  
  이렇게 하면 EC2 인스턴스와 EBS볼륨 그리고 custom software를 유지할 필요가 없음  
</deatils>
