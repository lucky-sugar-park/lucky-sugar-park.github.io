---
title: "AWS-SAP-CO2-Question-029"
last_modified_at: 2023-08-16T07:00:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---
<br/>
```
A company that has multiple AWS accounts is using AWS Organizations.  
The company’s AWS accounts host VPCs, Amazon EC2 instances, and containers.  
The company’s compliance team has deployed a security tool in each VPC where the company has deployments.  
The security tools run on EC2 instances and send information to the AWS account that is dedicated for the compliance team.  
The company has tagged all the compliance-related resources with a key of “costCenter” and a value or “compliance”.  
The company wants to identify the cost of the security tools that are running on the EC2 instances so that the company can charge the compliance team’s AWS account.  
The cost calculation must be as accurate as possible.  
What should a solutions architect do to meet these requirements?
```
<br/>
A. In the management account of the organization, activate the costCenter user-defined tag.  
   Configure monthly AWS Cost and Usage Reports to save to an Amazon S3 bucket in the management account.  
   Use the tag breakdown in the report to obtain the total cost for the costCenter tagged resources.
<br/>
B. In the member accounts of the organization, activate the costCenter user-defined tag.  
   Configure monthly AWS Cost and Usage Reports to save to an Amazon S3 bucket in the management account.  
   Schedule a monthly AWS Lambda function to retrieve the reports and calculate the total cost for the costCenter tagged resources.
<br/>
C. In the member accounts of the organization activate the costCenter user-defined tag.  
   From the management account, schedule a monthly AWS Cost and Usage Report.  
   Use the tag breakdown in the report to calculate the total cost for the costCenter tagged resources.
<br/>
D. Create a custom report in the organization view in AWS Trusted Advisor.  
   Configure the report to generate a monthly billing summary for the costCenter tagged resources in the compliance team’s AWS account.
<br/>
<details>
  <summary>정답</summary>
  site: A, community: A(94%)
  <br/>
  management account를 사용하는 것이 가장 합리적임. C도 답이 될 수 있을 것 처럼 보이지만, 
  조직의 마스터계정과 조직의 구성원이 단일계정에만 Billing and Cost Management 콘솔의 비용할당 태그 관리자에게 엑세스할 수 있음.  
  
  <br/>
  https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/custom-tags.html
</deatils>
