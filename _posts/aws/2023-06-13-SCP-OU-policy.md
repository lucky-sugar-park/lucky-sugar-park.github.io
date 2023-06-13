---
title: "Amazon SCP, OU, Policy"
last_modified_at: 2023-06-13T10:32:10-05:00
categories:
  - Blog
tags:
  - AWS SCP
  - AWS OU
  - AWS Policy
---

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d994d8f6-d262-43a5-a640-eeddffc08ff8)

개인이 AWS 계정을 사용하는 경우, Organization을 이용해 다른 AWS 계정들을 연동시키거나 Development, Staging, Production 등과 같은 OU를 따로 생성하는 경우는 별로 없을 것이다  
하지만 엔터프라이즈 급에서는 여려 가지 이유로 AWS Organization을 통해 다수의 AWS 계정을 연계해서 관리해야 한다.  

위의 그림은 AWS Organization에서 OU를 생성한 뒤에 OU에 만제 SCP를 적용한 후 AWS 계정들을 OU에 포함시키고 IAM 사용자들의 정책을 적용시킨 것을 나타낸다.  
AWS Organization - OU(s) - AWS. 사용자와 같은 구조가 되는 것이다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/72058fe1-d3ed-4fb4-adc4-2a9c23d94ef7)


### SCP (Service Control Policy)
- SCP는 OU 또는 AWS 계정을 대상으로 적용가능
- SCP는 하위 OU들에게 상속됨  
- SCP는 OU별로 다르게 적용 가능함  

### Permission Boundary & Policy
- 정책과 경계는 IAM 사용자 대상 적용 가능  
- SCP와 중첩되어야만 정책이 적용됨  

### OU (Organization Unit)  
- AWS 계정들의 그룹 단위  
- 하위 OU를 가질 수 있음  
- 재무팀, 구매팀, 개발팀, 운영팀 등
- 하나의 AWS 계정은 오직 하나의 OU에 속할 수 있다
