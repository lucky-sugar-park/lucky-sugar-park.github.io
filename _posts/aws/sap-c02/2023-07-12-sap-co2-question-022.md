---
title: "AWS-SAP-CO2-Question-022"
last_modified_at: 2023-07-12T07:22:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A software company has deployed an application that consumes a REST API by using Amazon API Gateway, AWS Lambda functions, and an Amazon DynamoDB table.
The application is showing an increase in the number of errors during PUT requests.
Most of the PUT calls come from a small number of clients that are authenticated with specific API keys.
A solutions architect has identified that a large number of the PUT requests originate from on client.
The API is noncritical, and clients can tolerate retries of unsuccessful calls.
However, the errors are displayed to customers and are causing damage to the API's reputation.
What should the solutions architect recommend to improve the customer experience?
```
<br/>
A. Implement retry logic with exponential backoff and irregular variation in the client application. Ensure that the errors are caugh and handled with descriptive error messages.  
<br/>
B. Implement API throtting through a usage plan at the API Gateway level. Ensure that the client application handles code 429 replies without error.  
<br/>
C. Turn on API caching to enhance responsiveness for the production stage. Run 10-minute load tests. Verify that the cache capacity is appropriate for the workload.  
<br/>
D. Implement reserved concurrency at the Lambda function level to provide the resources that are needed during sudden increases in traffic.
<br/>

<details>
  <summary>정답</summary>
  site: B, community: B(66%), A(32%)
  <br/>
  API throtting은 API로의 요청을 제어할 때에 사용할 수 있다. 적은 수의 클라이언트가 많은 (에러를 유발하는) 요청을 발생시키는 상황에서 유용하다.  
  API throtting을 API Gateway level에서 적용하면 하나의 클라이언트가 만드는 많은 수의 요청에 제한을 걸 수 있다. 이렇게 함으로써 에러의 수를 줄일 수 있다.  
  이 때에 클라이언트에게는 에러 메시지 없이 429번 코드를 보여줘야 한다. 이렇게 하면 고객에게 표시되는 에러의 수를 줄일 수 있다.  
  https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html
</deatils>
