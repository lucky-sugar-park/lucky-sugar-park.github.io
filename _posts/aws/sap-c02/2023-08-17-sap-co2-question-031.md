---
title: "AWS-SAP-CO2-Question-031"
last_modified_at: 2023-08-17T07:19:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
An enterprise company wants to allow its developers to purchase third-party software through AWS Marketplace.  
The company uses an AWS Organizations account structure with full features enabled, and has a shared services account in each organizational unit (OU)  
that will be used by procurement managers.  
The procurement team’s policy indicates that developers should be able to obtain third-party software from an approved list only  
and use Private Marketplace in AWS Marketplace to achieve this requirement.   
The procurement team wants administration of Private Marketplace to be restricted to a role named procurement-manager-role,  
which could be assumed by procurement managers.  
Other IAM users, groups, roles, and account administrators in the company should be denied Private Marketplace administrative access.  
What is the MOST efficient way to design an architecture to meet these requirements?
```
<br/><br/>
A. Create an IAM role named procurement-manager-role in all AWS accounts in the organization.   
   Add the PowerUserAccess managed policy to the role.  
   Apply an inline policy to all IAM users and roles in every AWS account to deny permissions on the AWSPrivateMarketplaceAdminFullAccess managed policy.
<br/><br/>
B. Create an IAM role named procurement-manager-role in all AWS accounts in the organization.  
   Add the AdministratorAccess managed policy to the role.  
   Define a permissions boundary with the AWSPrivateMarketplaceAdminFullAccess managed policy and attach it to all the developer roles.
<br/><br/>
C. Create an IAM role named procurement-manager-role in all the shared services accounts in the organization.  
   Add the AWSPrivateMarketplaceAdminFullAccess managed policy to the role.  
   Create an organization root-level SCP to deny permissions to administer Private Marketplace to everyone except the role named procurement-manager-role.  
   Create another organization root-level SCP to deny permissions to create an IAM role named procurement-manager-role to everyone in the organization.
<br/><br/>
D. Create an IAM role named procurement-manager-role in all AWS accounts that will be used by developers.  
   Add the AWSPrivateMarketplaceAdminFullAccess managed policy to the role.  
   Create an SCP in Organizations to deny permissions to administer Private Marketplace to everyone except the role named procurement-manager-role.  
   Apply the SCP to all the shared services accounts in the organization.
<br/><br/>
<details>
  <summary>정답</summary>
  site: D, community: C(96%)
  <br/>
  가장 효율적인 방안을 찾는 것임, 필요한 role을 생성하고 shared service account에 생성된 role을 할당.  
  <br/>
  adding the AWSPrivateMarketplaceAdminFullAccess managed policy to the role, the procurement managers will have the necessary permissions to administer Private Marketplace
  <br/>
  D라고 하는 의견도 있음 -> 근거 (https://aws.amazon.com/blogs/awsmarketplace/controlling-access-to-a-well-architected-private-marketplace-using-iam-and-aws-organizations/)
</deatils>
