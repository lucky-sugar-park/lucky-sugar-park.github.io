---
title: "AWS-SAP-CO2-Question-014"
last_modified_at: 2023-07-03T07:27:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is running an application on several Amazon EC2 instances in an Auto Scaling group behind an Application Load Balancer.  
The load on application varies throughout the day, and EC2 instances are scaled in and out on a regular basis.  
Log files from EC2 instances are copied to a central Amazon S3 bucket every 15 minutes.  
The security team discovers that log files are missing from some of the terminated EC2 instances.  
Which set of actions will ensure that log files are copied to the central S3 bucket from the terminated EC2 instances?
```
<br/>
A. Create a script to copy log files to Amazon S3, and store the script in a file on the EC2 instances.  
   Create an Auto Scaling lifecycle hook and an Amazon EventBridge rule to detect lifecycle events from the Auto Scaling group.  
   Invoke an AWS Lambda function on the autoscaling:EC2_INSTANCE_TERMINATING transition to send ABANDON to the Auto Scaling group to prevent termination, 
   run the script to copy the log files, and terminate the instance using the AWS SDK.  
<br/>
B. Create an AWS System Manager document with a script to copy log files to Amazon S3.  
   Create an Auto Scaling lifecycle hook and an Amazon EventBridge rule to detect lifecycle events from the Auto Scaling group.  
   Invoke an AWS Lambda function on the autoscaling:EC2_INSTANCE_TERMINATING transition to call the AWS System Manager API SendCommand operation to run the document to copy the log files and send CONTINUE to the Auto Scaling group to terminate the instance.  
<br/>
C. Change the log delivery rate to eveny 5 minutes. Create a script to copy log files to Amazon S3, and add the script to EC2 instance user data.  
   Create an Amazon EventBridge rule to detect EC2 istance termination. Invoke an AWS Lambda function from the EventBridge rule that uses the AWS CLI to run the user-data script to copy log files and terminate the instance.  
<br/>
D. Crate AWS System Manager document with script to copy log files to Amazon S3. Create an Auto Scaling lifecycle hook that publishs a message to an Amazon Simple Notification Sevice (Amazon SNS) topic.  
   From the SNS notification, call the AWS Systems Manager API SendCommand operation to run the document to copy the log files and send ABANDON to the Auto Scaling group to terminate the instance.  
<br/>

<details>
  <summary>정답</summary>
  B
  <br/>
  [EC2인스턴스 종료전 코드실행 설명참조](https://aws.amazon.com/blogs/infrastructure-and-automation/run-code-before-terminating-an-ec2-auto-scaling-instance/)  
  EC2 인스턴스가 종료되기 전에 로그파일을 S3에 복사할 수 있도록 구성해야 함.  
  Auto Scaling 라이프사이클 상에서 EC2가 종료되기 전에 해당 이벤트를 받아서 람다 Function을 구동시켜서 로그파일을 S3로 전송하도록 하고 나머지 종료 프로세스를 계속 진행하도록 함.  
  Auto Scaling 그룹에서 라이프사이클 이벤트를 검출해 내는 것은 Auto Scaling 라이프사이클 훅과 Amazon EventBridge 임.  
  **A번은 EC2 인스턴스의 종료를 허용하지 않는다고 했으므로 오답임**. 
  **C번은 로그수집 주기를 5분 단위로 한다고 했으므로 오답. 또한 user data를 사용하면 복잡도가 증가할 것임.**  
  **D번은 SNS 이야기가 불필요하게 갑자기 튀어 나왔음**
</deatils> 
