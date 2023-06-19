---
title: "AWS-SAP-CO@-Question-008"
last_modified_at: 2023-06-20T07:39:10-05:00
categories:
  - Blog
tags:
  
---

```
A company has a multi-tier web application that runs on a fleet of Amazon EC2 instances behind an Application Load Balancer (ALB).
The instances are in an Auto Sacling group. The ALB and the Auto Scaling group are replicated in a backup AWS Region.
The minimum value and the maximum value for the Auto Scaling group are set to zero. An Amzaon RDS Multi-AZ DB instance stores the application's data
The DB instance has a read replica in the backup Region.
The application presents an endpoint to end users by using an Amazon Route 53 record.
The company needs to reduce its RTO to less than 15 minutes by giving the application the ability to automatically fail over to backup Region.
The company does not have a large enough budget for an active-active strategy.
What should a solutions architect recommend to meet these requirement?
```
<br/><br/>
A. Reconfigure the applications' Route 53 record with a latency-based routing policy that load balances traffic between the two ALBs.  
   Create an AWS Lambda function in the backup Region to promote the read replica and modify the Auto Scaling group values.  
   Create an Amazon CloudWatch alarm that is based on the HTTPCode_Target_5XX_Count metric for the ALB in the primary Region.  
   Configure the CloudWatch alarm to invoke the Lambda function.  
<br/>
B. Create an AWS Lambda function in the backup Region to promote the read replica and modify the Auto Scaling group values.  
   Configure Route 53 with a health check that monitors the web application and sends an Amazon Simple Notification Service (Amazon SNS) notification to the Lambda function when the health check status is unhealthy.  
   Update the application's Route 53 record with a failover policy that route traffic to the ALB in the backup Region when a health check failure occurs.  
<br/>
C. Configure the Auto Scaling group in the backup Region to have the same values as the Auto Scaling group in the primary Region.  
   Reconfigure the application's Route 53 record with a latency-based routing policy that load balances traffic between the two ALBs.  
   Remove the read replica. Replace the read replica with a standalone RDS DB instance. 
   Configure Cross-Region Replication between the RDS DB instances by using snapshots and Amazon S3.  
<br/>
D. Configure an endpoint in AWS Global Accelerator with the two ALBs as equal weighted targets.  
   Create an AWS Lambda function in the backup Region to promote the read replica and modify th Auto Scaling group values.  
   Create an Amazon CloudWatch alarm that is based on th HTTPCode_Target_5XX_Count metric for the ALB in the primary Region.  
   Configure the CloudWatch alarm to invoke the Lambda function.  


<details>
  <summary>정답</summary>
  B<br/>
  [https://docs.amazonaws.cn/en_us/Route53/latest/DeveloperGuide/welcome-health-checks.html](https://docs.amazonaws.cn/en_us/Route53/latest/DeveloperGuide/welcome-health-checks.html)  
  [https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-types.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-types.html)  
  <br/>
  SNS + Health check  
  B가 15분 이하라는 RTO 요구와 Active-Active 구성할 예산이 없다는 요구조건을 만족시킬 수 있는 방법임  
  회사는 read replica를 발동시키는 백업 리전에 람다 Function을 생성하고 Aoto scaling value를 수정한다.  
  Route 53이 웹 어플리케이션을 모니터링 하도록 health check를 구성하고 unhealthy가 식별되면 Lambda function에게 SNS Notification을 전송한다.  
  Route 53 record가 또한 traffic을 백업 리전의 ALB로 전송하도록 failover 정책을 설정한다.  
  이렇게 함으로써 primary region이 먹통이 되면 failover 정책에 의해서 트래픽이 백업 리전으로 전달된다. (15분 RTO 이내)  
</deatils>
