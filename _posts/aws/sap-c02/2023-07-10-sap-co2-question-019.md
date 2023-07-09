---
title: "AWS-SAP-CO2-Question-019"
last_modified_at: 2023-07-10T08:35:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company has a serverless application comprised of Amazon CloudFront, Amazon API Gateway, and AWS Lambda functions.
The current deployment process of the application code is to create a new version number of the Lambda function and  
run an AWS CLI script to update.
If the new function version has errors, another CLI script reverts by deploying the previous working of the function.
The company would like to decrease the time to deploy new versions of the application logic provided by the Lambda functions,
and also reduce the time to detect and revert when errors are identified.
How can this be accomplishted?  
```
<br/>
A. Create and deploy nested AWS CloudFormation stacks with the parent stack consisting of the AWS CloudFront distribution and API Gateway, and the child stack containing the Lambda function.  
   For changes to Lambda, create an AWS CloudFormation change set and deploy; if errors are triggered, revert the AWS CloudFormation change set to the previous version.  
<br/>
B. Use AWS SAM and built-in AWS CodeDeploy to deploy the new Lambda version, gradually shift to the new version, and use pre-traffic and post-traffic test functions to verify code. Rollback if Amazon CloudWatch alarms are triggered.  
<br/>
C. Refactor the AWS CLI scripts into a single script that deploys the new Lambda version. When deployment is completed, the script tests execute. If errors are detected, revert to the previous Lambda version.  
<br/>
D. Create and deploy an AWS CloudFormation stack that consist of a new API Gateway endpoint that references the new Lambda version. Change the CloudFront origin to the new API Gateway endpoint, monitor errors and if detected, change the AWS CloudFront origin to the previous API Gateway endpoint.  
<br/>

<details>
  <summary>정답</summary>
  B
  <br/>
  SAM (Serverless Application Model)은 빌드-테스트-디플로이를 도와주는 프레임워크임.  
  만약 CloudFormation이 hood 상태에 있다면, CloudFormation 템플릿을 간단하게 생성, 갱신, 배포하는 방법이다.  
  CodeDeploy는 온프레미스 인스턴스와 람다펑션을 포함하는 어떤 인스턴스로의 배포를 자동화해주는 서비스이다.  
  SAM을 사용하면 SAM에 빌트인된 CodeDeploy를 사용해서 새로운 버전의 람다펑션을 배포할 수 있고 점차적으로 새로운 버전으로 트래픽을 옮길 수도 있다.  
  pre-traffic과 post-traffic 테스트도 (코드 유효성 검증을 위해) 진행할 수 있음  
  CloudWatch를 사용해서 어떤 케이스의 이슈라도 롤백할 수 있다.  
  이렇게 함으로써 좀 더 빠르고 효과적으로 배포를 진행할 수 있다. (에러 발생시 더욱 안정된 롤백 뿐 아니라)  
</deatils>
