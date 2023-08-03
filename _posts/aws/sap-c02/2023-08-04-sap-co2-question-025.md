---
title: "AWS-SAP-CO2-Question-025"
last_modified_at: 2023-08-04T07:27:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company uses an on-premises data analytics platform.  
The system is highly available in a fully redundant configuration across 12 servers in the company's data center.  
The system runs scheduled jobs, both hourly and daily, in addition to one-time requests from users.  
Scheduled jobs can take between 20 minutes and 2 hours to finish running and have tight SLAs.  
The scheduled jobs account for 65% of the system usage.
User jobs typically finish running in less than 5 minutes and have no SLA.  
The user jobs account for 35% of system usage.
During system failures, scheduled jobs must continue to meet SLAs.  
However, user jobs can be delayed.  
A solutions architect needs to move the system to Amazon EC2 instances and adopt a consumption-based model to reduce costs with no long-term commitments.  
The solution must maintain high availability and must not affect the SLAs.  
Which solution will meet these requirements MOST cost-effectively?
```
<br/>
A. Split the 12 instances across two Availability Zones in the chosen AWS Region. 
   Run two instances in each Availability Zone as On-Demand Instances with Capacity Reservations.  
   Run four instances in each Availability Zone as Spot Instances.  
<br/>
B. Split the 12 instances across three Availability Zone in the chosen AWS Region.  
   In one of the Availability Zones, run all four instances as On-Demand Instances with Capacity Reservations.  
   Run the remaining instances as Spot Instances.
<br/>
C. Split the 12 instances across three Availability Zones int the chosen AWS Region.  
   Run two instances in each Availability Zone as On-Demand Instances with a Saving Plan.  
   Run two intances in each Availability Zone as Spot Instances.  
<br/>
D. Split the 12 instnaces across three Availability Zones in the chosen AWS Region.  
   Run three instances in each Availability Zone as On-Demand Instances with Capacity Reservations.  
   Run one instance in each Availability Zone as a Spot Instance.
<br/>
<details>
  <summary>정답</summary>
  site: C, community: D(90%)
  <br/>
  35%가 사용자 요청 Job을 처리, 65%가 Scheduled Job 처리 (3개 Zone으로 나누면 각 Zone당 4개 Instance 임)  
  https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/amazon-ec2.html  
  Saving Plan: 절감형 플랜은 1년 또는 3년 시간당 지출 약정을 체결하는 것을 조건으로 온디맨드 가격대비 결제 금액을 최대 72% 줄일 수 있는 유연한 모델요금임.
  (EC2, Lambda, Fargate) => 예를 들어 한 시간에 $10의 컴퓨팅 사용량을 약정하면 $10까지 Saving Plan 가격이 청구되고 약정 이외의 사용량은 On-Demand 가격청구됨.  
  Spot Instance: 시간에 따라 변하는 인스턴스 가격, 즉 코인같이 실시간으로 비용이 변동되는 인스턴스를 해당 시간이나 가격에 따라 비용을 지불  
  보통 최대가격을 설정하여 해당 가격을 넘으면 인스턴스를 중지 (최고 가격을 지정하지 않으면 온디멘드 가격)

  AWS 스팟 인스턴스는 사전 약정 없이 온디멘드 요금보다 70~90% 절감된 비용으로 사용할 수 있는 EC2 인스턴스이다.  
  시작하려는 인스턴스에 대해 현재 시간에 유효한 가격만 지불하면 된다.  
  주로 배치잡 등 용도로 사용하는 서버에 잘 어울린다.  
  https://blog.leedoing.com/178 => 잘 설명해 놓았다 
</deatils>
