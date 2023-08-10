---
title: "AWS-SAP-CO2-Question-028"
last_modified_at: 2023-08-10T07:26:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company has registered 10 new domain names.  
The company uses the domains for online marketing.  
The company needs a solution that will redirect online visitors to a specific URL for each domain.  
All domains and target URLs are defined in a JSON document.  
All DNS records are managed by Amazon Route 53.
A solutions architect must implement a redirect service that accepts HTTP and HTTPS requests.  
Which combination of steps should the solutions architect take to meet these requirements with the LEAST amount of operational effort? (Choose three.)
```
<br/>
A. Create a dynamic webpage that runs on an Amazon EC2 instance. Configure the webpage to use the JSON document in combination with the event message to lookup and respond with a redirect URL.  
<br/>
B. Create an Application Load Balancer that includes HTTP and HTTPS listeners.  
<br/>
C. Create an AWS Lambda function that uses the JSON document in combination with the event message to look up and respond with a redirect URL.
<br/>
D. Use an Amazon API Gateway API with a custom domain to publishan AWS Lambda function.
<br/>
E. Create an Amazon CloudFront distribution. Deploy a Lambda@Edge function.
<br/>
F. Create an SSL certificate by using AWS certificate manager (ACM). Include the domains as Subject Alternative names.
<br/>
<details>
  <summary>정답</summary>
  site: BCF, community: CEF(65%), BDF(25%)
  <br/>
  A/B는 확실히 틀림 (redirect 하기 위해서 웹 서버를 유지하거나 설정을 해야 함-operational effort)  
  D도 오답인데, API Gateway API를 생성해야 하기 때문임

  E같은 경우 CloudFront는 CDN 서비스 인데, 이 것이 문제의 시나리오에 적합한지에 대한 의견이 갈릴 수 있음  
  Lambda@Edge는 content delivery를 최적화하기 위해서 edge location에 Lambda function을 구동하는 것을 허용함.  
</deatils>
