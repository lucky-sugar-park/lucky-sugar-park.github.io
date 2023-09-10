---
title: "AWS-SAP-CO2-Question-036"
last_modified_at: 2023-09-11T006:53:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---  
<br/>  
A company’s solutions architect is reviewing a web application that runs on AWS.   
The application references static assets in an Amazon S3 bucket in the us-east-1 Region.   
The company needs resiliency across multiple AWS Regions.   
The company already has created an S3 bucket in a second Region.
Which solution will meet these requirements with the LEAST operational overhead?    
<br/>   

A. Configure the application to write each object to both S3 buckets.   
   Set up an Amazon Route 53 public hosted zone with a record set by using a weighted routing policy for each S3 bucket.   
   Configure the application to reference the objects by using the Route 53 DNS name.   
<br/>
B. Create an AWS Lambda function to copy objects from the S3 bucket in us-east-1 to the S3 bucket in the second Region.   
   Invoke the Lambda function each time an object is written to the S3 bucket in us-east-1.   
   Set up an Amazon CloudFront distribution with an origin group that contains the two S3 buckets as origins.   
<br/>
C. Configure replication on the S3 bucket in us-east-1 to replicate objects to the S3 bucket in the second Region.   
   Set up an Amazon CloudFront distribution with an origin group that contains the two S3 buckets as origins.   
<br/>
D. Configure replication on the S3 bucket in us-east-1 to replicate objects to the S3 bucket in the second Region.   
   If failover is required, update the application code to load S3 objects from the S3 bucket in the second Region.  
<br/>

<details>
  <summary>정답</summary>
  site: D, community: C(94%)
  <br/>
  가장 operational overhead가 적은 것을 선택하는 것임.   
  D의 경우 어플리케이션 코드를 수정해야 함.   
  https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html 참조  
  위 문서에 의하면 CloudFront는 S3 bucket을 Primary와 Secondary로 지정할 수 있으며, 문제 발생 시에 자동으로 Failover 됨   

  [그림참조](https://docs.aws.amazon.com/images/AmazonCloudFront/latest/DeveloperGuide/images/origingroups-overview.png)
  <br/>
</deatils>
