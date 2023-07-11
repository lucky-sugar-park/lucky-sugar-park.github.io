---
title: "AWS-SAP-CO2-Question-021"
last_modified_at: 2023-07-11T16:44:10-05:00
categories:
  - Blog
tags:
  - AWS-SAP-CO2
---

```
A company is using an on-premises Active Directory service for user authentication.
The company wants to use the same authentication service to sign in to the company's AWS accounts, which are using AWS organizations.
AWS Site-to-Site VPN connectivity already exists between the on-premises environment and all the company's AWS accounts.
The company's security policy requires conditional access to the accounts based on use groups and roles.
User identities must be managed in a single location.
Which solution will meet these requirements?  
```
<br/>
A. Configure AWS IAM Identity Center (AWS Single Sign-On) to connect to Active Directory by using SAML 2.0.Enable automatic provisioning by using the System for Cross-domain Identity Management (SCIM) v2.0 protocol. Grant access to the AWS accounts by using attribute-based access controls (ABACs).  
<br/>
B. Configure AWS IAM Identity Center (AWS Single Sign-On) by using IAM Identity Center as an identity source.  Enable automatic provisioning by using the System for Cross-domain Identity Management (SCIm) v2.0 protocol. Grant access to the AWS accounts by using IAM Identity Center permission sets.
<br/>
C. In one of the company’s AWS accounts, configure AWS Identity and Access Management (IAM) to use a SAML 2.0 identity provider. Provision IAM users that are mapped to the federated users. Grant access that corresponds to appropriate groups in Active Directory. Grant access to the required AWS accounts by using cross-account IAM users  
<br/>
D. In one of the company’s AWS accounts, configure AWS Identity and Access Management (IAM) to use an OpenID Connect (OIDC) identity provider. Provision IAM roles that grant access to the AWS account for the federated users that correspond to appropriate groups in Active Directory. Grant access to the required AWS accounts by using cross-account IAM roles.  
<br/>
<details>
  <summary>정답</summary>
  site: D, community: A(82%)
  <br/>
  https://www.examtopics.com/discussions/amazon/view/74174-exam-aws-certified-solutions-architect-professional-topic-1/  
  C/A가 위 문제의 시나리오에 유효한 옵션임.  
  ABAC는 사용자/리소스/액션 속성기반으로 리소스 접근을 제어하는 방법임. 이 방법으로 하면 세밀하게 접근제어를 수행할 수 있으며, 사용자나 그룹기반 계정의 조건에 따른 접근제어를 세밀하게 할 수 있음  
  AWS IAM (AWS SSO)를 사용하면 온 프레미스의 Active Directory와 온 프레미스를 연결할 수 있다 (SAML 2.0방식으로) 이렇게 함으로써 Cross domain identity management (SCIM) v2.0 프로토콜을 사용하여 시스템을 자동으로 프로비저닝할 수 있다.  
  SCIM v2.0은 단일 로케이션에서 사용자 ID를 관리할 수 있도록 해 줌
</deatils>
