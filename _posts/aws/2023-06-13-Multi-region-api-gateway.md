---
title: "Multi region strategty for API Gateway"
last_modified_at: 2023-06-13T08:09:10-05:00
categories:
  - Blog
tags:
  - AWA API Gateway
  - Route 53
  - CloudFront
---

만약 DR에 (Disaster recovery)에 대한 요구가 없다면, 글로벌 커스토머를 위한 최적의 API Gateway 정책은 무엇일까?  
<br/>
아래와 같이 4가지 옵션이 있을 수 있다 (비용을 고려하지 않는다면 4번째 옵션이 BEST가 되지 않을까?)  

- **옵션 1**: Single Edge Optimized API Gateway  
  - 장점: 비용절감, 복잡성 감소 (데이터 복제 필요 없음)  
  - 단점: Latency 증가 (글로별 고객의 요구는 가장 가까운 Edge로 몰릴 것이기 때문)  
- **옵션 2**: Multiple Regional API Gateway with Route 53 Latency based routing  
  - 장점: 응답속도 향상 (트래픽은 가장 가까운 리전으로 전달)  
  - 단점: 데이터 복제에 따른 데이터 량 증가, 비용증가. 대표하는 Front가 없기 때문에 트래픽은 가까운 리전의 API로 몰림. 이럴 경우 특정 리전에 트래픽이 몰릴 수 있음 (자칫 옵션 1보다 응답성능이 떨어질 수 있음)  
- **옵션 3**: Multiple Edge Optimized API Gwateway with Route 53 Latency based routing
  - 장점: customers hitting closest API. Not sure how latency based routing works on an edge optimized endpoint, would it even help, since both endpoints are edge optimized.  
  - 단점: 데이터 복제, 비용
- **옵션 4**: Multiple Regional API Gateway with single custom Cloundfront on top with cloudfront functions to do the routing 
  - 장점: customers hitting closest edge optimized location and routed to nearest API, this routing will be based on country of origin header from cloudfront  
  - 단점: 데이터 복제, 비용
