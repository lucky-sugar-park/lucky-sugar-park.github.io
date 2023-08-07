---
title: "AWS-SAP-CO2-Question-027"
last_modified_at: 2023-08-08T07:51:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is storing data in several Amazon DynamoDB tables.  
A solutions architect must use a serverless architecture to make the data accessible publicly throhgh a simple API over HTTPS.  
The solution must scale automatically in response to demand.  
Which solutions meet these requirements? (Choose two.)
```
<br/>
A. Create an Amazon API Gateway REST API. Configure this API with direct integration to DynamoDB by using API Gateway's AWS integration type.
<br/>
B. Create an Amazon API Gateway HTTP API. Configure this API with direct integration to DynamoDB by using API Gateway's AWS integration type.
<br/>
C. Create an Amazon API Gateway HTTP API. Configure this API with integration to AWS Lambda functions that return data from the DynamoDB tables.
<br/>
D. Create an accelerator in AWS Global Accelerator. Configure this accelerator with AWS Lambda@Edge function integrations that return data from the DynamoDB tables.
<br/>
E. Create a Network Load Balancer. Configure listener rules to forward requests to the appropriate AWS Lambda functions.
<br/>
<details>
  <summary>정답</summary>
  site: CD, community: AC(89%)
  <br/>
  [설명](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-overview-developer-experience.html)  
  API Gateway REST API can invoke DynamoDB directly.      
  DynamoDb is not one of the supported services for HTTP API.
  HTTP API로는 다이나모DB에 직접 접근할 수 없다 (B는 불가능함)  
  HTTP API is a light-weighted REST API that only supports two types of backend,  
  Lambda and HTTP while REST API supports three backend: Lambda, HTTP and AWS services (DynamoDB for example).  
  Source: https://medium.com/@fengliplatform/api-gateway-talks-to-dynamodb-in-two-ways-f45356c87986
</deatils>
