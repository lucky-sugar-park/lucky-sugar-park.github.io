---
title: "AWS-SAP-CO2-Question-026"
last_modified_at: 2023-08-07T07:27:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A security engineer determined that an existing application retrieves credentials to an Amazon RDS for MySQL database from an eccrypted file in Amazon S3.  
For the next version of the application, the security engineer wants to implement the following application design changes to improve security:  
- The database must use strong, randomly generated passwords stored in a secure AWS managed service.  
- The application resources must be deployed through AWS CloudFormation.  
- The application must rotate credentials for the database every 90 days.  
- A solution architect will generate a CloudFormation template to deploy the application.
Which resources specified in the CloudFormation template will meet the security engineer's requirements with the LEASTamount of operational overhead?
```
<br/>
A. Generate the database password as a secret resource using AWS Secret Manager.   
   Create an AWS Lambda function resource to rotate the database password.  
   Specify a Secrets Manager RotationSchedule resource to rotate the databse passwordevery 90 days.  
<br/>
B. Generate the databse password as a SecureString parameter type using AWS Systems Manager Parameter Store.  
   Create an AWS Lambda function resource to rotate the database password.  
   Specify a Parameter Store RotationSchedule resource to rotate the database password every 90 days.  
<br/>
C. Generate the database password as a secret reource using AWS Secrets manager.  
   Create an AWS Lambda function resource to rotate the database password.  
   Create an Amazon EventBridge scheduled rule resource to trigger the Lambda function password roation every 90 days.  
<br/>
D. Generate the database password as a SecureString parameter type using AWS Systems Manager Parameter Store.  
   Specify an AWS AppSync DataSource resource to automatically rotate the database password every 90 days.  
<br/>
<details>
  <summary>정답</summary>
  site: B, community: AD(100%)
  <br/>
  https://docs.aws.amazon.com/secretsmanager/latest/userguide/cloudformation.html  
  ParameterStore does not support automated rotation of secrets instead of saying ParameterStore::RotationSchedule is not supported by CF.    
  Option C is wrong. It does not meet the requirement because it does not use CloudFormation.  
  Option D is wrong. The AWS::AppSync::DataSource resource is what to create data sources for resolvers in AWS AppSync to connect to  
  A가 답인 것 같은데, 왜 람다를 사용해야만 할까?  
  Amazon RDS는 마스터 유저의 Credentials를 위한 managed rotation을 제공한다.  
  For more information, see Password management with Amazon RDS and AWS Secrets Manager in the Amazon RDS User Guide
</deatils>
 
