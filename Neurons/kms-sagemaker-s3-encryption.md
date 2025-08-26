---

title: kms-sagemaker-s3-encryption
created: 2025-08-26
modified: 2025-08-26
tags:
- service/kms
- service/sagemaker
- service/s3
- security/encryption
- permission/iam
aliases: ["kms-encryption", "sagemaker-kms", "s3-kms-access"]

---

# KMS로 암호화된 S3 데이터를 SageMaker에서 접근하는 권한 설정

## 🎯 핵심 포인트

KMS로 암호화된 S3 버킷의 데이터에 SageMaker 노트북 인스턴스가 접근해야 하는 경우, IAM 역할에 S3 읽기 권한과 KMS 키 정책에서 해당 역할에 대한 복호화 권한을 모두 부여해야 한다.

## 📝 설명

### KMS 암호화가 필요한 이유

AWS KMS(Key Management Service)는 데이터 암호화를 위한 완전 관리형 서비스로, 민감한 ML 데이터셋을 안전하게 보호할 수 있다. S3에 저장된 데이터를 KMS로 암호화하면 저장 시점부터 전송 과정까지 데이터 보안을 보장할 수 있다.

### KMS 권한 구조의 이해

KMS는 **서비스 연동**과 **사용자 권한** 두 단계로 구성된다:

1. **서비스 연동**: 어떤 AWS 서비스에서 KMS 키를 사용할지 결정 (S3, RDS, EBS 등)
2. **사용자 권한**: 누가 그 키를 사용할 수 있는지 결정 (IAM 역할, 사용자 등)

### 아키텍처 플로우

```
ML Specialist → S3 Bucket (KMS 암호화) → SageMaker Notebook
     ↓               ↓                        ↓
  데이터 업로드    암호화 저장           IAM Role + KMS 권한
     ↓               ↓                        ↓
  KMS Key Policy ← IAM Role Policy ← SageMaker Execution Role
```

### KMS 설정 순서

1. **KMS 키 생성**: 데이터 암호화용 키 생성
2. **KMS 키 별칭**: 키 식별을 위한 별칭 설정 (선택사항)
3. **KMS 키 정책**: 어떤 Principal(역할/사용자)이 키를 사용할지 정의
4. **IAM 역할 정책**: S3 접근 + KMS 복호화 권한 부여
5. **S3 버킷 암호화**: 기본 암호화 설정
6. **데이터 업로드**: KMS 키로 암호화하여 업로드
7. **SageMaker 설정**: IAM 역할을 노트북 인스턴스에 연결

### Trade-offs 고려사항

**KMS 암호화 장점**:
- 데이터 보안 강화 (저장/전송 중 암호화)
- 세밀한 접근 제어 가능
- 키 회전 자동화 지원
- 감사 로그 제공

**KMS 암호화 단점**:
- 추가 비용 발생 (API 호출당 과금)
- 설정 복잡성 증가
- 약간의 성능 오버헤드
- 키 관리 책임

**일반 S3 암호화(SSE-S3) 장점**:
- 간단한 설정
- 추가 비용 없음
- AWS에서 자동 관리

**일반 S3 암호화(SSE-S3) 단점**:
- 세밀한 접근 제어 불가
- 키 회전 제어 불가
- 크로스 계정 접근 복잡

## 🔍 주요개념

### 권한 유형 비교

- **KMS 키 정책**: 리소스 기반 정책으로 누가 키를 사용할 수 있는지 정의
- **IAM 역할 정책**: 신원 기반 정책으로 해당 역할이 어떤 작업을 수행할 수 있는지 정의

### 실전 적용

- **ML 파이프라인**: 훈련 데이터셋을 KMS로 암호화하여 SageMaker에서 안전하게 처리
- **규제 준수**: 금융/의료 데이터 처리 시 암호화 요구사항 충족
- **크로스 계정 접근**: 다른 AWS 계정의 SageMaker에서 암호화된 데이터 접근

## 📝 관련 문제

**Question:** A Machine Learning Specialist uploads a dataset to an Amazon S3 bucket protected with server-side encryption using AWS KMS. How should the ML Specialist define the Amazon SageMaker notebook instance so it can read the same dataset from Amazon S3?

**Options:**

- A) Define security group(s) to allow all HTTP inbound/outbound traffic and assign those security group(s) to the Amazon SageMaker notebook instance
- B) Configure the Amazon SageMaker notebook instance to have access to the VPC. Grant permission in the KMS key policy to the notebook's KMS role
- C) Assign an IAM role to the Amazon SageMaker notebook with S3 read access to the dataset. Grant permission in the KMS key policy to that role
- D) Assign the same KMS key used to encrypt data in Amazon S3 to the Amazon SageMaker notebook instance

**정답: C) Assign an IAM role to the Amazon SageMaker notebook with S3 read access to the dataset. Grant permission in the KMS key policy to that role**

💡 추가 설명:

- **A번** - 보안 그룹은 네트워크 수준 접근 제어로 S3 API 접근과 무관하며, 모든 트래픽 허용은 보안상 위험
- **B번** - S3는 VPC와 독립적인 서비스로 VPC 접근 권한이 S3 데이터 읽기와 직접적 관련 없음
- **D번** - KMS 키는 인스턴스에 직접 할당되지 않으며, IAM 권한 없이는 여전히 S3 접근 불가

---