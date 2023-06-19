---
title: "AWS Beanstalk or ECS"
last_modified_at: 2023-06-19T14:30:02-05:00
categories:
  - Blog
tags:
  - AWS Beanstalk
  - AWS ECS
---

[참조사이트](https://www.missioncloud.com/blog/resource-docker-containers-on-aws-use-elastic-beanstalk-or-elastic-container-services)

> 컨테이너화-(각 어플리케이션의 전체 VM을 구동하지 않고) 분산화된 어플리케이션을 배포, 구동할 때 사용되는 가상화된 방법-는 클라우드 환경에서 어플리케이션들을 개발하고 배포하는 방식을 바꾸고 있다.
> 컨테이너들은 어플리케이션들을 구동에 필요한 모든 것들을 (코드, 코드 데이타, 설정파일, 인터페이스, 종속된 것들) 포함하는 작고 관리가능한 패키지로 쪼갠다.

컨테이너 방식의 접근을 함으로써 개발자들은  배포와 인프라에 신경쓸 필요 없이 어플리케이션에만 집중할 수 있다. 개발측면에서 보면 컨테이너식 접근을 함으로써 많은 혜택을 얻을 수 있다.  
- 개발 파이프라인을 가속화 시킨다. 여기에는 테스팅과 디버깅이 포함된다.
- CI (Continuous Integration)와 CD (Continuous Deployment) 워크플로우를 촉진시킨다. 이렇게 함으로써 새로운 코드가 커밋될 때마다 자동으로 빌드되도록 할 수 있다. 
- 컨테이너들은 데스크탑 또는 랩탑 상에서 구동될 수도 있고, 클라우드 상에 직접적으로 쉽게 업로드 된다.
- 개발계에서 테스트로 테스트에서 프로덕션으로 옮길 때에 일관성이 유지된다.
- OS별, 클라우드 플랫폼 별로 코드를 재 작성할 필요가 없다 (컨테이너를 다른 클라우드 프로바이더로 쉽게 옮길 수 있다)

개발 사이클을 넘어서도 컨테이너의 잇점을 찾을 수 있다.  
컨테이너는 하이퍼바이저의 필요성을 제거함으로써 컴퓨터 자원을 보다 효율적으로 사용한다.  
컨테이너는 단지 OS 커널을 공유할 뿐이다.  
공유할 때에 컨테이너 내에서 구동되고 있는 어플리케이션의 성능에 영향을 미치지도 않는다.  
게다가 컨테이너는 요구되는 바이너리와 컴포넌트만으로 구성될 수 있다.  
이것은 전체 OS가 설치된 버전에서 발견되는 잠재적 취약점을 제거할 수 있도록 한다.  
Amazon EC2에 올라가는 컨테이너는 On-Demand 방식 가격대비 90% 이상을 절감할 수 있다.  

## 도커 컨테이너를 AWS에 배포하기  
도커 소프트웨어 플랫폼은 어플리케이션을 빌드, 테스트, 배포하는 가장 유명한 방법 중의 하나이다. 아마존은 도커 컨테이너를 AWS 상에서 배포할 때에 두 가지 옵션을 제공한다. Elastic Beanstalk과 Elastic Container Service가 그것이다. 둘 사이의 주된 차이점은 컨테이너의 제어 수준에 있다. 각각은 어플리케이션 스케일링, 용량, 스케쥴링에 있어서 각기 다른 제어수준을 보여준다.  

#### Elastic Beanstalk  
Elastic Beanstalk은 스케일링 가능하도록 웹 어플리케이션을 배포하기 위한 AWS 서비스이다. 이것을 사용하면 어플리케이션을 구동하는데에 필요한 AWS 리소스를 수동으로 구동할 필요가 없다. IDE와 AWS 관리콘설 또는 Git 리포지터리를 사용하면 도커 컨테이너를 업로드할 수 있다.  
<br/>
Elastic Beanstalk은 컨테이너 배포, 요구되는 인프라스트럭처의 프로비저닝, 플랫폼 관리등을 (가장 최근 패치제공, 어플리케이션 업데이트 등) 제공한다.  
<br/>
Elastic Beanstalk 콘솔을 사용하면 어플리케이션을 관리할 수 있고, 단일 유닛으로 시작/정지를 할 수 있다. Auto-Scaling 기능을 사용하면 어플리케이션의 필요할 때에 up/down scale 된다. 또한 클러스터 간의 분산된 컨테이너들 간 자동으로 부하분산이 이루어 지도록 할 수 있다.  

#### Elastic Container Service 
[Elastic Container Service (ECS)](https://aws.amazon.com/ko/ecs/) 는 도커 컨테이너 지원을 위한 오케스트레이션 서비스이다. API call을 함으로써, 수만개의 도커 컨테이너를 관리하고 구동할 수 있다. ECS는 Virtual Machine을 Scaling하고 관리하며 이런 VM 상의 컨테이너들을 스케쥴링한다. 또한 VM의 가용성을 유지한다.  
<br/>
ECS는 서버 프로비저닝의 필요성을 제거하고 컨테이너들을 관리/배고할 때에 [AWS Fargate](https://aws.amazon.com/ko/fargate/) 에 의존적이다. ECS는 넓은 범위의 컨테이너화된 어플리케이션을 지원한다. (from long running to microservice). 또한 리거시 리눅스 또는 윈도우 어플리케이션의 클라우드 상에서 구동될 수 있도록 마이그레이션을 지원한다.  
<br/>
ECS를 사용하면 아마존 VPC에서 구동함으로써 세밀한 보안 컨트롤을 할 수 있다 (VPC security group과 network ACL을 사용) 또한 IAM을 사용하면, 어떤 서비스, 어떤 컨테이너를 접근할 수 있도록 할지를 결정할 수 있다.  
<br/>
ECS를 사용하면 (native Integration을 통해) Elastic Load Balancing, Elastic Container Registry, AWS Batch, CloudWatch, CloudFormation, CloudTrail과 같은 서비스를 받을 수 있다는 잇점이 있다.  
<br/>

## 언제 Elastic Beanstalk를 사용하는 것이 최선일가? 
만약 AWS 또는 컨테이너 개념에 처음이거나 익숙하지 않다면, 심플하게 도커를 시작할 수 있고 새로운 어플리케이션을 개발할 수 있는 Elastic Beanstalk가 도커 컨테이너를 적용하기 위한 최선의 접근이 될 수 있다.  
<br/>
Elastic Beanstalk는 간단한 인터페이스를 제공하며 도커 이미지를 public/private 저장소에서 가져오고 Amazon ECS 클러스터에 도커 컨테이너를 배포할 수 있다.  
<br/>
Elastic Beanstalk를 사용하면 어플리케이션의 스케일링과 능력에 대한 제어수단을 덜 제공하지만, 도커 컨테이너를 AWS 상에 직관적으로 배포할 수 있다.  
<br/>

## 언제 Elastic Container Service를 사용하는 것이 최신일까?  
Elastic Beanstalk과 비교해서 ECS는 도커 컨테이너에 대한 오케스트레이션과 어플리케이션 아키텍처에 더 많은 제어 수단을 제공한다.  클러스터 노드의 수와 크기를 명시할 수 있고 auto scaling의 사용여부를 결정할 수도 있다.  
<br/>
ECS는 도커 컨테이너를 구동하기 위해서 task라는 개념을 사용한다. Task에는 컨테이너 정의, 컨테이너들이 함께에 시작하고 동시에 종료되는 컨테이너 그룹과 같은 능력이 포함된다.  
<br/>
ECS는 CPU와 메모리 스케쥴링에 대한 상당한 Customization과 유연성을 제공한다. 게다가 ECS는 다른 AWS 서비스와의 통합을 위한 특별한 노력을 요구하지 않는다.  
<br/>

ECS는 다음과 같은 경우에 적합하다.  
- 다른 AWS 서비스와 통합이 요구되는 마이크로 서비스
- EC2의 On-Demand, Reserved, Spot Instance 상의 Batch 워크로드를 구동하기 위한 Custom/관리형 스케쥴러를 사용하는 경우.
- 리거시 코드를 컨테이너로 옮기고 이를 코드를 수정하지 않고 AWS로 옮기는 경우
- 여러 가지 플랫폼에서 구동되는 Loosely coupled, distrubuted service로 구성된 어플리케이션 또는 워크플로우
- 분산화된 데이터 소스를 접근할 때에  
<br/>

## 도커 컨테이너를 AWS에 배포할 때에는 전문가의 컨설팅을 받는 것이 좋다  


