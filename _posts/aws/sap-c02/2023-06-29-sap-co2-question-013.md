---
title: "AWS-SAP-CO2-Question-012"
last_modified_at: 2023-06-29T07:26:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company needs to implement a patching process for its servers.
The on-premises and Amazon EC2 instances use a variety of tools to perform patching.
Management requires a single report showing the patch status of all the servers and instances.
Which set of actions should a solutions architect take to meet these requirements? 
```
<br/>
A. Use AWS System Manager to manage patches on the on-premises servers and EC2 instances. Use System Manager to generate patch compliance reports.  
<br/>
B. Use AWS OpsWorks to manage patches on the on-premises servers and EC2 instances. Use Amazon QuickSight integration with OpsWorks to generate patch compliance reports.  
<br/>
C. Use an Amazon EventBridge rule to apply patches by scheduling an AWS System Manager patch remediation job. Use Amazon Inspector to generate patch compliance reports.  
<br/>
D. Use AWS OpsWorks to manage patches on the on-premises servers and EC2 instances. Use AWS X-Ray to post the patch status to AWS System Manager OpsCenter to generate patch compliance reports.
<br/>

 <details>
  <summary>정답</summary>
  A
  <br/>
  [Patch management 설명참조](https://docs.aws.amazon.com/prescriptive-guidance/latest/patch-management-hybrid-cloud/design-on-premises.html)  
  AWS System Manager는 on-premises servers와 EC2 인스턴스 패치를 관리할 수 있고 리포트를 생성할 수 있다.  
  AWS OpsWorks and Amazon Inspector are not specifically designed for patch management  
  and therefore would not be the best choice for this use case.  
  Using Amazon EventBridge rule and AWS X-Ray to generate patch compliance reports is not a practical solution  
  as they are not designed for patch management reporting.
</deatils> 
