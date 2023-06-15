---
title: "AWS Aurora"
last_modified_at: 2023-06-15T08:17:10-05:00
categories:
  - Blog
tags:
  - AWS
  - Aurora
  - RDS
---

## Aurora 란
- Amazon Aurora는 RDS (Relation Database Service)의 일부로써, MySQL 및 PostgreSQL과 호환되는 **완전 관리형 (서버리스)** 관계형 데이터베이스 엔진임  
- 기존 어플리케이션을 거의 변경하지 않고도 MySQL 처리량의 최대 5배, PostgreSQL 처리량의 최대 3배 능력
- 고성능 스토리지 하위 시스템이 포함됨 (기본 스토리지는 필요에 따라 자동으로 확장됨, 클러스터 볼륨 크기는 최대 128TiB까지 증가 가능
- 데이터베이스 클러스터링 및 복제를 자동화하고 표준화 함  

## Aurora 특징
1. MySQL/PostgreSQL 지원
   - 두 가지 모드 지원
     - 다수의 노드로 읽기, 쓰기가 가능한 Multi-Master
     - 한 개의 쓰기 전용 노드와, 다수의 읽기 전용 노드 (Autora Replicas) 구성의 Single Master
   - Multi-Master는 장점이 있지만, 이것을 사용하면 Aurora 기능 중 일부를 사용할 수 없음 (대부분 Single Master 사용)
2. 용량의 자동 증감
   **RDS는 EBS 기반이라 따로 용량을 설정**해야 하는 반면에 Aurora의 경우 10GB부터 시작해서 10GB 단위로 용량 증가 가능 (최대 128TB).
   이는 RDS와는 다르게 일기 쓰기 노드와 저장 노드를 분리시켰기 때문에 생기는 장점임
3. 연산능력
   96vCPU와 768GB까지 증가 가능 (db.r5.24xlarge)
4. 데이터 분산저장
   각 AZ마다 2개의 데이터 복제본 저장, 3개 이상의 AZ로 총 6개의 복제본 생성이 가능
   - 3개 이상을 잃어버리기 전에는 쓰기능력 유지
   - 4개 이상을 잃어버리기 전에는 읽기능력 유지
   - 손실된 복제본은 자가 치유됨: 지속적으로 손실된 부분을 검사 후 복구함 (읽기 노드와 저장 노드가 분리되어 있기 때문에 가능함)
   - Quorum  모델 (투표 시스템: 총 6개의 복제본 상태를 어떻게 유지하느냐에 대한 방법)  

## Aurora의 두 가지 모델 
1. Single-Master 모드
   - 한 대의 Writer 인스턴스와 다수의 읽기 전용 인스턴스 (Aurora Replicas)로 구성
   - 총 15개의 Replica 생성 가능
   - Async 복제
   - 하나의 Region 안에 생성 가능
   - Writer가 죽을 경우 자동으로 Replica 중 하나가 Writer로 Failover (Failover시 데이터 손실 없음)
   - 고 가용성 확보
2. Multi-Master 모드
   - 최대 4개의 노드가 읽기/쓰기를 담당 (각 노드는 독립적이기 때문에 정지/재부팅/삭제 등에 상호 영향을 받지 않음)
   - 지속적인 가용성 제공 (Continuous Availability)
   - 주로 Multi-tenant 혹은 Sharding이 적용된 어플리케이션에서 좋은 성능을 발휘함
  
## Aurora Global Database
- 전세계의 모든 리전에서 1초내에 데이터 엑세스가 가능함
- 재해복구용도로 활용 가능
  - 유사시 보조 리전 중 하나를 승격하여 메인으로 활용
  - 1초의 RPO (복구목표지점)
  - 1분 미만의 RTO (복구목표시간)
- 보조 리전에는 총 16개의 Read 전용노드 생성이 가능함

## Auto scaling 
Auto Saclig 정책에서 1) Aurora Read Replica이 평균 CPU 사용률, 2) Aurora Read REplica 평균연결 수 기준으로 Scaling 기준 설명 가능  
- 평균 CPU 사용률: 전체 Read Replica들의 CPU 사용률 평균 값
- 평균 연결수: 전체 Read Replica들의 Database connection의 평균 값
<br/><br/>
Auto Scaling을 적용하게 되면 CloudWatch에서 측정치를 실시간으로 감시한다.  CloudWatch는 리소스를 모니터링 하며, Scale up/down 기준 값 충족시 해당 메커니즘을 실행 시킨다

1. Aurora Scale-in vs Scale-out
   - AWS Auto Scaling은 보수적으로 동작하도록 설계 (Scale-out: 빠른 확장, Scale-in: 느린 축소)
   - Scale-out 메커니즘은 목표 값을 기준으로 구동된다
   - Scale-in은 목표 값의 90% 혹은 Scale-up의 근접한 값 기준으로 결정되며, 이는 Aurora 내부 설정 값이기 때문에 설정/변경 불가능함
2. Scale-out
   - CloudWatch는 실시간으로 CPU 사용률을 추적하며 Scale-out 기준 값 도달 시 알람을 발생시킴
   - CloudWatch로부터 발생한 알림을 전달받은 Aurora는 Scale-out 메커니즘 발현시킴
   - Scale-out 되는 기준은 목표 값이며 가용성을 보장하기 위해 Metric에 비례하게 빠르게 증가함
   - **만약 여러 정책이 적용되어 있는 경우 한개라도 충족되면 진행됨**
3. Scale-in
   - 어플리케이션 가용성 보장을 위해 Metric에 비례하여 느리게 감소되도록 설계됨
   - CloudWatch는 실시간으로 CPU 사용률을 추적하고 Scale-in 기준 충족시 알림 발생
   - CloudWatch로부터 발생한 알림을 전달 받은 Aurara는 Scale-in 발현
   - 가용성을 보장하기 위해 Scale-in은 보수적으로 동작됨. 기준은 일반적으로 목표 값보나 10% 정도 낮은 값으로 설정되며, 이 기준이 15분 동안 유지되었을 때 Scale-in 발현됨
   - 목표 값의 90%, 15분의 속성 값은 Aurora 내부적으로 설계된 값이며, 사용자 설정이 불가함
   - 만약 **여러 정책이 적용되어 있다면 모든 조건이 충족되어야만** Scale-in 이 발현됨  
