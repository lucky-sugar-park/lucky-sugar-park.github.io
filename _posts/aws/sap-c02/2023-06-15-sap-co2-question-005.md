---
title: "Question-005"
last_modified_at: 2023-06-15T07:20:10-05:00
categories:
  - Blog
tags:
---

```
A company uses a service to collect metadata from applications that the company hosts on premises.
Consumer devices such as TVs and internet radios access the applications.
Many older devices do not support certain HTTP headers and exhibit errors when these headers are present in responses.
The company has configured an on-premises load balancer to remove the unsupported headers from responses sent to older devices, which the company identified by the User-Agent headers.

The company wants to migrate the service to AWS, adopt serverless technologies, and retain the ability to support the older devices.
The company has already migrated the applications into a set of AWS Lambda functions.
Which solution will meet these requirements?  
```

A. Create an Amazon CloudFront distribution for metadata service. Create an Application Load Balancer(ALB). Configure the CloudFront distribution to forward requests to the ALB. Configure the ALB to invoke the correct Lambda function for each type of request. Create a CloudFront function to remove the problemetic headers based on the value of the User-Agent header.  
<br/>
B. Create Amazon API Gateway REST API for the metadata service. Configure API Gateway to invoke the correct Lambda function for each type of request. Modify the default gateway resp[onses to remove the problematic headers based on the value of the User-AGent header.  
<br/>
C. Create an Amazon API Gateway HTTP API for the metadata service. Configure API Gateway to invoke the correct Lambda function for each type of request. Create a response mapping template to remove the problematic headers based on the value of the User-Agent. Associate the response data mapping with the HTTP API.  
<br/>
D. Create an Amazon CloudFront distribution for the metadata service. Create an Application Load Balancer (ALB). Configure the CloudFront distribution to forward requests to the ALB. Configure the ALB to invoke the correct Lambda function for each type of request. Create a Lambda@Edge function that will remove the problematic headers in response to viewer requests based on the value of the User-Agent header  

<details>
  <summary>정답</summary>
  A(45%), D(26%), C(16%), B(14%)<br/>
  A와 D의 차이점은 CloudFront function vs Lambda@Edge 임. 이 경우에 CloudFront function 이 더 빠르고 가벼운 방법으로 헤더의 일부 내용을 제거할 수 있음  
  => Examtopics에서는 A를 정답이라고 한 사람이 가장 많은데, Lambda@Edge를 통해서 Header의 일부 정보를 제거할 수 있는 것으로 소개하고 있음  
</deatils>
