---
title: "Question-001"
last_modified_at: 2023-06-13T08:09:10-05:00
categories:
  - Blog
tags:
  - CloudFront
---

## CloudFront 란 (CDN 서비스) 
Amazon CloudFront는 .html, .css, .js 및 이미지 파일과 같은 정적 및 동적 웹 콘텐츠를 사용자에게 더 빨리 배포하도록 지원하는 웹 서비스임. (CDN 서비스)  
CloudFront는 엣지 로케이션이라고 하는 데이터 센터의 전 세계 네트워크를 통해 콘텐츠를 제공함.  
CloudFront를 통해 서비스하는 콘텐츠를 사용자가 요청하면 지연 시간이 가장 낮은 엣지 로케이션으로 요청이 라우팅됨. 
<br/>
- 콘텐츠가 이미 지연 시간이 가장 낮은 엣지 로케이션에 있는 경우 CloudFront가 콘텐츠를 즉시 제공
- 콘텐츠가 엣지 로케이션에 없는 경우 CloudFront는 콘텐츠의 최종 버전에 대한 소스로 지정된 오리진(Amazon S3 버킷, MediaPackage 채널, HTTP 서버(예: 웹 서버) 등)에서 콘텐츠를 검색

예를 들어, CloudFront가 아닌 일반적인 웹 서버에서 이미지를 제공한다고 가정하면, https://example.com/sunsetphoto.png URL을 사용하여 sunsetphoto.png라는 이미지를 서비스할 수 있다.  
사용자는 이 URL로 쉽게 이동해 해당 이미지를 볼 수 있다. 하지만 이미지가 발견될 때까지 인터넷으로 이루어진 상호 연결된 네트워크의 복잡한 모음을 통해 네트워크에서 다른 네트워크로 요청이 라우팅되었다는 사실은 아마도 모르고 있을 것이다.  
CloudFront는 AWS 백본 네트워크를 통해 콘텐츠를 가장 효과적으로 서비스할 수 있는 엣지로 각 사용자 요청을 라우팅하여 콘텐츠 배포 속도를 높인다. 일반적으로 CloudFront 엣지가 최종 사용자에게 가장 빨리 제공한다. AWS 네트워크를 사용하면 사용자의 요청이 반드시 통과해야 하는 네트워크의 수가 줄어들어 성능이 향상된다. 파일의 첫 바이트를 로드하는 데 걸리는 지연 시간이 줄어들고 데이터 전송 속도가 빨라진다.

또한 파일(객체라고도 함)의 사본이 전 세계 여러 엣지 로케이션에 유지(또는 캐시)되므로 안정성과 가용성이 향상된다.

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/1ef1989d-5ae9-4ce2-ae7d-6b89a8cdec0d)  

### 좀 더 쉬운 말로 (AWS는 말을 너무 어렵게 함)  
CloudFront는 AWS에서 제공하는 CDN 서비스 임. 캐싱을 통해 사용자에게 좀 더 빠른 전송속도 제공을 목적으로 함. 전 세계 이곳 저곳에 엣지서버를 (Location)을 두고 Client에 가장 가까운 엣지서버를 찾아 Latency를 최소화시켜 데이터를 빠르게 제공한다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/96784714-688f-4303-b866-d1db22a841ac)  

- Origin Server: 원본 데이터를 가지고 있는 서버임. 보통 AWS에서의 Origin Server는 S3, EC2 Instance 임.
- Edge Server: Edge Location과 같은 의미, AWS에서 실질적으로 제공하는 전 세계에 퍼져 있는 서버. Origin Server에 대한 Cache 기능 제공

  1. 클라이언트로부터 Edge Server로의 요청이 발생함
  2. Edge Server는 요청이 발생한 데이터에 대해 캐싱 여부를 확인함
  3. 사용자와 가까운 거리에 위치한 Edge Server중 캐싱 데이터가 존재하면 사용자의 요청에 맞는 데이터를 응답함
  4. 사용자의 요청에 적합한 데이터가 캐싱되어 있지 않다면 Origin Server로 요청이 포워딩 됨.
  5. 요청 받은 데이터에 대해 Origin Server에서 획득한 후, Edge Server에 캐싱 데이터를 생성하고, 클라이언트로 응답함

## 서비스 대상 컨텐츠
- Downlad Distribution : HTTP 프로토콜을 이용해서 다운로드할 수 있는 일반적인 이미지 혹은 정적 파일을 제공받을 수 있다.
- Streaming Distribution : 스트리밍을 위해 사용할 수 있는  HTTP Proressive DownLoad 방식이나 RTSP(Real Time Streaming Protocol)을 지원하는 동영상 콘텐츠를 서비스 받을 수 있다.

## Edge Server 특징
1. 기본적으로 한번 발생한 요청에 대해서는 Edge Server에 캐싱된 상태로 저장  
2. Edge Server의 기본 TTL은 24시간이고 사용자의 설정에 따라 변경가능함 (TTL 수정 시 Edge Server 반영시간은 1시간 정도)  
   - 캐시 설정 후 반영시간 때문에, 전체 데이터에 대한 TTL을 수정하지 않고 개별 데이터에 대한  Invalidation API (특정 파일을 캐시에서 삭제하는 기능)을 통해 삭제 가능함  
   - Invalidation API는 동시에 최대 3개의 요청을 발생시킬 수 있으며, 각 요청은 최대 1,000개까지 가능함
   - Invalidation API는 Edge Node에 반영되기까지 5~10분 정도 소요됨  

## 더 많은 정보 
https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-CloudFront-%EA%B0%9C%EB%85%90-%EC%9B%90%EB%A6%AC-%EC%82%AC%EC%9A%A9-%EC%84%B8%ED%8C%85-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC


