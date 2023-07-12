---
title: "AWS-SAP-CO2-Question-023"
last_modified_at: 2023-07-13T07:31:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is running a data-intensive application on AWS.
The application runs on a cluster of hundreds of Amazon EC2 instances.
A shared file system also runs on several EC2 instances that store 200 TB of data.
The application reads and modifies the data on the shared file system and generates a report.
The job runs once monthly, reads a subset of the files from the shared file system, and takes about 72 hours to complete.
The compute instances scale in an Auto Scaling group, but the instances that host the shared file system run continuously.
The compute and storage instances are all in the same AWS Region.
A solutions architect needs to reduce costs by replacing the shared file system instances.
The file system must provide high performance access to the needed data for the duration of the 72-hour run.
Which solution will provide the LARGEST overall cost reduction while meeting these requirements?
```
<br/>
A. Migrate the data from the existing shared file system to an Amazon S3 bucket that uses the S3 Intelligent-Tiering storage class. Before the job runs each month, use Amazon FSx for Lustre to create a new file system with the data from Amazon S3 by using lazy loading. Use the new file system as the shared storage for the duration of the job. Delete the file system when the job is complete.  
<br/>
B. Migrate the data from the existing shared file system to a large Amazon Elastic Block Store (Amazon EBS) volume with Multi-Attach enabled. Attach the EBS volume to each of the instances by using a user data script in the Auto Scaling group launch template. Use the EBS volume as the shared storage for the duration of the job. Detach the EBS volume when the job is complete  
<br/>
C. Migrate the data from the existing shared file system to an Amazon S3 bucket that uses the S3 Standard storage class. Before the job runs each month, use Amazon FSx for Lustre to create a new file system with the data from Amazon S3 by using batch loading. Use the new file system as the shared storage for the duration of the job. Delete the file system when the job is complete.  
<br/>
D. Migrate the data from the existing shared file system to an Amazon S3 bucket. Before the job runs each month, use AWS Storage Gateway to create a file gateway with the data from Amazon S3. Use the file gateway as the shared storage for the job. Delete the file gateway when the job is complete.  
<br/>

<details>
  <summary>정답</summary>
  site: D, community: A(94%)
  <br/>
  A의 Lazy loading 방식은 비용에 효과적인 방식임
  C의 경우 배치방식으로 하면 너무 많은 데이터가 될 것임
  D의 경우 온프레미스 데이터에 접근하기 위해서 스토리지 게이트웨이가 사용되는데, 아마존에서 권장하는 방식이 아님
</deatils>
