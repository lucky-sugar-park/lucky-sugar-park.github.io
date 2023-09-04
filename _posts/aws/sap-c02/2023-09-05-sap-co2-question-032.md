---
title: "AWS-SAP-CO2-Question-032"
last_modified_at: 2023-09-05T07:27:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

A company is in the process of implementing AWS Organizations to constrain its developers to use only Amazon EC2, Amazon S3, and Amazon DynamoDB.   
The developers account resides in a dedicated organizational unit (OU).   
The solutions architect has implemented the following SCP on the developers account:   
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEC2",
            "Effect": "Allow",
            "Action": "ec2:*",
            "Resource": "*"
        },
        {
            "Sid": "AllowDynamoDB",
            "Effect": "Allow",
            "Action": "dynamodb:*",
            "Resource": "*"
        },
        {
            "Sid": "AllowS3",
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}
```   
When this policy is deployed, IAM users in the developers account are still able to use AWS services that are not listed in the policy.   
What should the solutions architect do to eliminate the developers’ ability to use services outside the scope of this policy?   
<br/>
A. Create an explicit deny statement for each AWS service that should be constrained.   
<br/>
B. Remove the FullAWSAccess SCP from the developers account’s OU.    
<br/>
C. Modify the FullAWSAccess SCP to explicitly deny all services.   
<br/>
D. Add an explicit deny statement using a wildcard to the end of the SCP.   
<br/><br/>

<details>
  <summary>정답</summary>
  site: A, community: B(79%),D(17%)
  <br/>
  뭐가 답인지 정확히 모르겠음    
  <br/>
</deatils>
