---

title: iam-groups-ml-team-management
created: 2025-08-19
modified: 2025-08-19
tags:
- service/iam
- constraint/team-management
- usecase/machine-learning
- security/access-control
- deployment/multi-user
aliases: ["iam-groups", "team-access", "ml-permissions"]

---

# IAM Groups를 활용한 머신러닝 팀 접근 권한 관리

## 🎯 핵심 포인트

여러 팀 멤버가 참여하는 머신러닝 프로젝트의 경우 IAM Groups에서, 역할별 그룹을 생성하여 체계적인 권한 관리를 할 수 있다.

## 📝 설명

### IAM Groups가 팀 기반 프로젝트에 적합한 이유

IAM Groups는 동일한 역할을 가진 사용자들을 논리적으로 묶어서 권한을 일괄 관리할 수 있는 AWS의 핵심 보안 서비스입니다. 머신러닝 프로젝트에서는 데이터 사이언티스트, ML 엔지니어, 프로젝트 매니저 등 서로 다른 역할이 서로 다른 AWS 리소스에 접근해야 하므로, 그룹 기반 권한 관리가 필수적입니다. 각 그룹에는 해당 역할에 필요한 최소한의 권한만 부여하여 보안 원칙을 준수하면서도 업무 효율성을 보장할 수 있습니다.

### 아키텍처 플로우

```
ML Project Team
├── ML-DataScientists Group
│   ├── User: Alice
│   ├── User: Bob
│   └── Permissions: SageMaker, S3 Data Access, CloudWatch
│
├── ML-Engineers Group
│   ├── User: Charlie  
│   ├── User: Diana
│   └── Permissions: EC2, Lambda, API Gateway, Docker Registry
│
└── ML-Managers Group
    ├── User: Eve
    └── Permissions: Cost Explorer, IAM Users, Resource Monitoring
```

### Trade-offs 고려사항

**IAM Groups 장점**:
- 확장 가능한 권한 관리 (새 팀원 추가 시 그룹에만 포함)
- 일관된 권한 부여 (같은 역할 = 같은 권한)
- 중앙화된 정책 관리 (그룹 단위 권한 변경)
- 감사 및 컴플라이언스 용이성

**IAM Groups 단점**:
- 초기 설정 복잡성
- 그룹 구조 설계 시 신중한 계획 필요
- 권한 상속으로 인한 예상치 못한 접근 권한

**개별 IAM User 방식 장점**:
- 세밀한 개별 권한 제어
- 사용자별 맞춤 정책 적용

**개별 IAM User 방식 단점**:
- 관리 복잡성 증가 (사용자 수에 비례)
- 일관성 부족으로 인한 보안 위험
- 정책 변경 시 개별 적용 필요

## 🔍 주요개념

### 권한 관리 방식 비교

- **IAM Groups**: 역할 기반으로 사용자들을 그룹화하여 권한을 일괄 관리하는 방식
- **IAM Roles**: 임시적이고 상황별 권한 부여를 위한 방식 (Cross-account, 서비스간 접근)
- **IAM Users**: 개별 사용자에게 직접 권한을 부여하는 방식

### 실전 적용

- **데이터 사이언티스트 그룹**: SageMaker 노트북, S3 데이터 버킷, Athena 쿼리 권한
- **ML 엔지니어 그룹**: EC2 인스턴스, Lambda 함수, ECR 도커 이미지, API Gateway 권한  
- **DevOps 팀 그룹**: CloudFormation, CodePipeline, CloudWatch, 전체 인프라 관리 권한

## 📝 관련 문제

**Question:** You are responsible for managing a machine learning project that involves several team members. You need to ensure that each team member has the appropriate access to the AWS resources required to complete their tasks. Which of the following is the best practice for managing access to AWS resources in this scenario?

**Options:**

- A) Create a single IAM role that grants all team members access to the required AWS resources
- B) Grant each team member access to the root account and let them manage their own IAM permissions
- C) Create an IAM user for each team member and provide them with the necessary access keys to access the AWS resources
- D) Create an IAM group for each team and assign the necessary permissions to each group. Then, add team members to the appropriate group

**정답: D) Create an IAM group for each team and assign the necessary permissions to each group**

💡 추가 설명:

- **Option A** - 단일 IAM Role은 모든 팀원이 동일한 권한을 가져 최소 권한 원칙 위배
- **Option B** - Root 계정 공유는 최고 수준의 보안 위험을 초래하는 절대 금기사항
- **Option C** - 개별 IAM User 관리는 확장성이 떨어지고 일관성 유지가 어려움