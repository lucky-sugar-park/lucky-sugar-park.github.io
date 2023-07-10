---
title: "AWS-SAP-CO2-Question-020"
last_modified_at: 2023-07-11T08:31:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is planning to store a large number of archived documents and make the documents available to employees through the corporate intranet.
Employees will access the system by connecting through a client VPN service that is attached to a VPC.
The data must not be accessible to the public.
The documents that the company is storing are copies of data that is held on physical medial elsewhere.
The number of requests will be low.
Availability and speed of retrieval are not concerns of the company.
Which solution will meet these requirements at the LOWEST cost?  
```
<br/>
A. Create an Amazon S3 bucket. Configure the S3 bucket to use the S3 One Zone-Infrequent Access (S3 One Zone-IA) storage class as default. Configure the S3 bucket for website hosting. Create an S3 interface endpoint. Configure the S3 bucket to allow access only through that endpoint.  
<br/>
B. Launch an Amazon EC2 instance that runs a web server. Attach an Amzaon Elastic File System (Amazon EFS) file system to store the archived data in the EFS One Zone-Infrequent Access (EFS One Zone-IA) storage class Configure the instance security group to allow access only from private networks.  
<br/>
C. Launch an Amazon EC2 instance that runs a web server Attach an Amazon Elastic Block Store (Amazon EBS) volume to store the archived data. Use the Cold HDD(sc1) volume type. Configure the instance security groups to allow access only from private networks.  
<br/>
D. Create an Amazon S3 bucket. Configure the S3 bucket to use the S3 Glacier Deep Archive storage class as default. Configure the S3 bucket for website hosting. Create an S3 interface endpoint. Configure the S3 bucket to allow access only through that endpoint. 
<br/>

<details>
  <summary>정답</summary>
  Site: D, Community: A(64%), D(35%)
  <br/>
  (아무리 속도가 중요하지 않다고 하더라도) Glacire Deep Archive는 웹 호스팅에 사용될 수 없다. - Deep Archive를 사용하면 조회에만 수시간이 걸릴 수도 있다.  
  (아마도 Glacier Deep Archive가 가장 저렴한 듯)  
  Deep Archive와 S3 bucket을 함께 사용하면 속도 문제를 개선할 수 있나?  (웹 호스팅에 Deep Archive를 사용할 수 있다고 함)  
  https://tutorialsdojo.com/amazon-s3-vs-glacier/  
  https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects.html  
  D는 "no public access" 라는 조건 때문에 배제되어야 함 (비용은 A가 더 많이 나옴)  
</deatils>
