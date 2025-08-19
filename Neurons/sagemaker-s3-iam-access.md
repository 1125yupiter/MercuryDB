---

title: sagemaker-s3-iam-access
created: 2025-08-19
modified: 2025-08-19
tags:
- service/sagemaker
- service/s3
- security/iam
- constraint/default-role
- access/bucket-naming
aliases: ["sagemaker-s3", "notebook-s3-access", "iam-s3-bucket"]

---

# SageMaker 노트북 인스턴스의 S3 접근 권한 관리

## 🎯 핵심 포인트

SageMaker 노트북 인스턴스가 기본 IAM 역할을 사용하는 경우, 이름에 "sagemaker"가 포함된 S3 버킷에만 접근할 수 있으며, 추가적인 S3 접근을 위해서는 S3FullAccess 정책을 추가하거나 커스텀 정책을 적용할 수 있다.

## 📝 설명

### SageMaker가 S3 접근 제어에 적합한 이유

SageMaker의 기본 IAM 역할은 AWS의 최소 권한 원칙(Principle of Least Privilege)을 구현하여 보안을 강화합니다. 기본적으로 "sagemaker" 문자열이 포함된 버킷에만 접근을 허용함으로써, 데이터 과학자가 실수로 민감한 데이터에 접근하는 것을 방지하면서도 필요한 작업은 수행할 수 있도록 설계되었습니다.

### 아키텍처 플로우

```
SageMaker Notebook Instance
    ↓ (기본 IAM 역할)
IAM Role 권한 확인
    ↓
S3 버킷 이름 검증
    ↓
"sagemaker" 포함? → YES → 접근 허용
    ↓
    NO → 접근 거부 (403 Forbidden)
    ↓
추가 정책 (S3FullAccess) → 모든 S3 접근 허용
```

### Trade-offs 고려사항

**기본 IAM 역할 장점**:
- 보안성: 최소 권한으로 무분별한 데이터 접근 방지
- 자동 설정: 별도 구성 없이 즉시 사용 가능
- 규칙 기반: 명확한 네이밍 규칙으로 접근 범위 예측 가능

**기본 IAM 역할 단점**:
- 제한적 접근: 기존 버킷 활용 시 제약
- 네이밍 강제: 버킷명에 "sagemaker" 포함 필수
- 유연성 부족: 프로젝트별 세밀한 권한 제어 어려움

**S3FullAccess 정책 장점**:
- 전체 접근: 모든 S3 버킷과 객체에 접근 가능
- 유연성: 기존 데이터 파이프라인과 쉬운 통합

**S3FullAccess 정책 단점**:
- 보안 위험: 과도한 권한으로 데이터 유출 가능성
- 규정 준수: 엄격한 보안 요구사항 충족 어려움

## 🔍 주요개념

### IAM 기반 접근 제어 vs 다른 방식

- **IAM 역할**: 노트북 인스턴스에 부여된 권한으로 S3 접근 제어 (AWS 표준)
- **VPC 엔드포인트**: 네트워크 연결성 관리, 접근 권한과는 별개
- **버킷 ACL**: 개별 버킷 수준 권한, 중앙 집중식 관리 어려움

### 실전 적용

- **개발 환경**: "my-sagemaker-dev-bucket" 형태로 버킷 생성하여 기본 역할 활용
- **프로덕션 환경**: 특정 버킷만 접근 가능한 커스텀 IAM 정책 생성
- **데이터 레이크 통합**: 기존 S3 데이터 레이크 버킷에 대한 읽기 전용 권한 추가

## 📝 관련 문제

**Question:** A data scientist has recently initialized a new Amazon SageMaker notebook instance for a predictive modeling project, utilizing the default IAM role provided during setup. Considering the need to access datasets stored in an Amazon S3 bucket, what method governs the notebook instance's ability to interact with S3 data?

**Options:**

- A) Unlimited access to all S3 buckets is granted by default
- B) Access is limited to S3 buckets with "sagemaker" in the name, unless S3FullAccess is added
- C) S3 access is managed through a VPC Endpoint, not IAM policies
- D) Access to S3 requires manual configuration of each bucket's ACL

**정답: B) Access is limited to S3 buckets with "sagemaker" in the name, unless S3FullAccess is added**

💡 추가 설명:

- **옵션 A** - AWS는 최소 권한 원칙을 따르며, 기본적으로 모든 S3 버킷에 무제한 접근을 허용하지 않음
- **옵션 C** - VPC 엔드포인트는 네트워크 연결을 위한 것으로, IAM 기반 접근 제어와는 별개의 개념
- **옵션 D** - SageMaker의 S3 접근은 주로 IAM 역할로 관리되며, 개별 버킷 ACL 설정이 주요 방법은 아님