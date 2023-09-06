---
title: "AWS-SAP-CO2-Question-034"
last_modified_at: 2023-09-06T07:39:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

A company has created an OU in AWS Organizations for each of its engineering teams.    
Each OU owns multiple AWS accounts.   
The organization has hundreds of AWS accounts.   

A solutions architect must design a solution so that each OU can view a breakdown of usage costs across its AWS accounts.   
Which solution meets these requirements?

A. Create an AWS Cost and Usage Report (CUR) for each OU by using AWS Resource Access Manager.   
   Allow each team to visualize the CUR through an Amazon QuickSight dashboard.   
<br/>
B. Create an AWS Cost and Usage Report (CUR) from the AWS Organizations management account.   
   Allow each team to visualize the CUR through an Amazon QuickSight dashboard.   
<br/>
C. Create an AWS Cost and Usage Report (CUR) in each AWS Organizations member account.   
   Allow each team to visualize the CUR through an Amazon QuickSight dashboard.  
<br/>
D. Create an AWS Cost and Usage Report (CUR) by using AWS Systems Manager.    
   Allow each team to visualize the CUR through Systems Manager OpsCenter dashboards.
<br/>
<details>
  <summary>정답</summary>
  site: B, community: B(100%)
  <br/>
  B 가 정답.   
  The solution would be to create an AWS Cost and Usage Report (CUR) from the AWS Organizations management account. This would allow the management account to view the usage costs across all the member accounts, and the teams can visualize the CUR through an Amazon QuickSight dashboard. This allows the organization to have a centralized place to view the cost breakdown and the teams to access the cost breakdown in an easy way.   
  <br/>
  참조할만한 글: [https://taehyeki.tistory.com/379   ](https://aws.amazon.com/blogs/mt/visualize-and-gain-insights-into-your-aws-cost-and-usage-with-cloud-intelligence-dashboards-using-amazon-quicksight/)https://aws.amazon.com/blogs/mt/visualize-and-gain-insights-into-your-aws-cost-and-usage-with-cloud-intelligence-dashboards-using-amazon-quicksight/
</deatils>
