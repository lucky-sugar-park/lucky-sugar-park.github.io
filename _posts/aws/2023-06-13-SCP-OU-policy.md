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
- 하나의 AWS 계정은 오직 하나의 OU에 속할 수 있음  

### Managenent Account
- OU를 생성한 계정  
- 다른 AWS Account를 초대하거나 OU에서 포함시키거나 제외시킬 수 있음  
- 오직 하나의 마스터/루트 계정만 존재함  
- 이 계정은 SCP의 영향을 받지 않음  

### Account 
- OU에 초대받은 AWS 계정들 
- AWS 계정들은 OU에 초대 받을 수 있음  

### IAM User 
- AWS 계정에 속하는 IAM 사용자  
- Permission Boundary와 Policy에 직접적인 영향을 받음  

### AWS Organization 사용시 장점
- 중앙관리와 거버넌스 일관성 
  - 다수의  AWS 계정들을 일관성 있게 관리 (그룹 또는 OU를 구성한 뒤 적절한 SCP를 연결해 다수의 계정들을 사용게 만제 일관적으로 관리 가능)
  - 다른 계정들의 다른 서버, 스토리지 및 클라우드 리소스를 효과적으로 관리 
  - 기업의 구조와 동일하게 관리 가능  

- 접근제어 단순화  
  - 계정 그룹을 만든 다음 그룹에 SCP를 연결하여 계정 전체에 적절한 정책이 적용되도록 할 수 있음  
  - 개별 AWS 서비스를 구체적으로 허용 또는 거부할 수 있음

- **통합결제**
  - 통합결제를 통해 조직의 모든 AWS 계정에 대해 단일결제 방법을 설정할 수 있음  
  - 모든 계정에서 발생한 요금의 통합보기, 개별적 계정 추적, 비용 데이터 다운로드  
  - 하나의 대시보드에서 모든 계정의 비용관리 및 감시 가능  

- 중앙집중식 보안 및 감사
  - AWS CloudTail을 사용하여 계정의 모든 이벤트 감사  
  - 권장 구성기준을 중앙에서 정의할 수 있음  
  - 리소스, AWS 리전 및 AWS Config가 있는 계정 전반에 걸쳐 AWS Control Tower를 사용하여 교차 계정 보안감사를 설정하거나 계정 전체에 적용된 정책을 관리하고 볼 수 있음   - GuardDuty, Firewall Manager, IAM Access Analyzer 등과 같은 보안 서비스를 중앙에서 관리하여 리소스를 보호할 수 있음  
