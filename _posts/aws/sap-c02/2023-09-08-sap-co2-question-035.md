---
title: "AWS-SAP-CO2-Question-035"
last_modified_at: 2023-09-08T011:09:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

A company is storing data on premises on a Windows file server.   
The company produces 5 GB of new data daily.   
The company migrated part of its Windows-based workload to AWS and needs the data to be available on a file system in the cloud.   
The company already has established an AWS Direct Connect connection between the on-premises network and AWS.   
Which data migration strategy should the company use?   
<br/>
A. Use the file gateway option in AWS Storage Gateway to replace the existing Windows file server, and point the existing file share to the new file gateway.  
<br/>
B. Use AWS DataSync to schedule a daily task to replicate data between the on-premises Windows file server and Amazon FSx.  
<br/>
C. Use AWS Data Pipeline to schedule a daily task to replicate data between the on-premises Windows file server and Amazon Elastic File System  (Amazon EFS).   
<br/>
D. Use AWS DataSync to schedule a daily task to replicate data between the on-premises Windows file server and Amazon Elastic File System (Amazon EFS).   
<br/>
<details>
  <summary>정답</summary>
  site: B, community: B(69%),A(31%)
  <br/>
  B와 D가 유효한 옵션   
  DataSync를 적용해서 파일데이터를 마이그레이션 할 수 있음.   
  EFS는 Linux 파일 시스템일 경우에 적용할 수 있음. FSx는 윈도우에서도 적용할 수 있음   
  <br/>
</deatils>
