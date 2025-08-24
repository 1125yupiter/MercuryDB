---

title: sagemaker-kms-encryption-tracking
created: 2025-08-24
modified: 2025-08-24
tags:
- service/kms
- service/sagemaker
- security/encryption
- compliance/tracking
- data/sensitive
aliases: ["kms-tracking", "sagemaker-encryption", "key-audit"]

---

# SageMaker에서 KMS를 활용한 민감 데이터 암호화 및 키 추적

## 🎯 핵심 포인트

민감한 소비자 정보를 처리하는 ML 모델에서 AWS가 키 관리의 신뢰 루트 역할을 하면서 키 사용 이력을 추적해야 하는 경우, AWS KMS의 Customer Managed Keys를 사용하여 완전한 암호화와 감사 추적을 구현할 수 있다.

## 📝 설명

### AWS KMS가 SageMaker 민감 데이터 암호화에 적합한 이유

AWS KMS(Key Management Service)는 클라우드에서 암호화 키를 중앙 집중식으로 관리하는 완전 관리형 서비스입니다. SageMaker와 통합하여 ML 데이터 볼륨과 S3의 모델 아티팩트를 암호화하며, CloudTrail과의 완벽한 통합을 통해 모든 키 사용 활동을 자동으로 추적하고 감사할 수 있습니다.

### 아키텍처 플로우

```
[민감 데이터] → [S3 버킷 (KMS 암호화)] 
     ↓
[SageMaker 트레이닝] ← [Customer Managed Key (KMS)]
     ↓                           ↓
[암호화된 모델 아티팩트] → [CloudTrail 로깅]
     ↓                           ↓
[SageMaker 엔드포인트] → [키 사용 이력 추적]
```

### Trade-offs 고려사항

**AWS KMS Customer Managed Keys 장점**:
- AWS가 키 관리의 루트 오브 트러스트 역할 수행
- CloudTrail을 통한 완전한 키 사용 이력 추적
- SageMaker 및 S3와의 네이티브 통합
- 자동 키 로테이션 및 정책 기반 접근 제어
- 규제 요구사항 충족을 위한 감사 로그 제공

**AWS KMS 단점**:
- API 호출당 비용 발생
- 지역별 키 관리 필요
- 초당 요청 제한 존재

**SageMaker 내장 임시 키 장점**:
- 추가 비용 없음
- 설정이 간단함

**SageMaker 내장 임시 키 단점**:
- 키 사용 이력 추적 기능 제한적
- 중앙집중식 키 관리 불가능
- 규제 요구사항 충족 어려움

**AWS CloudHSM 장점**:
- 전용 하드웨어 보안 모듈 제공
- FIPS 140-2 Level 3 검증

**AWS CloudHSM 단점**:
- 높은 비용과 복잡한 설정
- 키 사용 추적을 위한 별도 구현 필요
- SageMaker와의 통합이 복잡함

## 🔍 주요개념

### 키 관리 옵션 비교

- **Customer Managed Keys**: 사용자가 정책과 권한을 완전히 제어하며, CloudTrail을 통한 상세한 감사 로그 제공
- **AWS Managed Keys**: AWS 서비스별로 자동 생성되지만 사용자 제어 제한적
- **Customer Provided Keys**: 사용자가 키를 직접 제공하지만 AWS 외부 관리 필요

### 실전 적용

- **금융 서비스**: 고객 신용정보 기반 ML 모델에서 규제 준수를 위한 키 추적
- **헬스케어**: 환자 데이터 처리 시 HIPAA 규정 충족을 위한 암호화 및 감사
- **이커머스**: 개인정보 기반 추천 시스템에서 GDPR 요구사항 충족

## 📝 관련 문제

**Question:** A business will use Amazon SageMaker to train and host a machine learning model for a marketing campaign. The bulk of data is classified as sensitive consumer information. At rest, the data must be encrypted. The organization wishes for AWS to act as the root of trust for the master keys and to keep track of encryption key use. Which implementation will satisfy these criteria?

**Options:**

- A) Use encryption keys that are stored in AWS CloudHSM to encrypt the ML data volumes, and to encrypt the model artifacts and data in Amazon S3.
- B) Use SageMaker built-in transient keys to encrypt the ML data volumes. Enable default encryption for new Amazon Elastic Block Store (Amazon EBS) volumes.
- C) Use customer managed keys in AWS Key Management Service (AWS KMS) to encrypt the ML data volumes, and to encrypt the model artifacts and data in Amazon S3.
- D) Use AWS Security Token Service (AWS STS) to create temporary tokens to encrypt the ML storage volumes, and to encrypt the model artifacts and data in Amazon S3.

**정답: C) Customer managed keys in AWS KMS**

💡 추가 설명:

- **Option A (CloudHSM)** - 전용 HSM을 제공하지만 설정이 복잡하고 키 사용 추적을 위한 별도 구현이 필요하며, 요구사항보다 과도한 솔루션
- **Option B (내장 임시 키)** - 키 사용 이력 추적 기능이 제한적이며 중앙집중식 키 관리가 불가능하여 요구사항 미충족
- **Option D (STS)** - 임시 자격 증명 서비스로 암호화 키 관리 서비스가 아니며, 키 사용 추적 기능 없음