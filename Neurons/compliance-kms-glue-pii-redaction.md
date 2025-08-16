---
title: compliance-kms-glue-pii-redaction
created: 2025-08-16
modified: 2025-08-16
tags:
  - service/kms
  - service/glue
  - service/sagemaker
  - constraint/compliance
  - usecase/pii-redaction
aliases:
  - AWS-ML-Security
  - KMS-Glue-SageMaker
  - PII-Redaction
---

# AWS KMS와 Glue를 활용한 머신러닝 데이터 보안 및 컴플라이언스

## 🎯 핵심 포인트

민감한 개인정보(PII)가 포함된 데이터셋으로 머신러닝 모델을 개발하는 경우 AWS KMS로 암호화하고 AWS Glue로 민감정보를 마스킹하여 컴플라이언스를 준수할 수 있다.

## 📝 설명

### AWS KMS와 Glue가 ML 보안에 적합한 이유

**AWS KMS(Key Management Service)**는 데이터 암호화를 위한 완전관리형 서비스로, S3 저장소와 SageMaker 노트북 인스턴스의 ML 스토리지 볼륨을 모두 암호화할 수 있습니다. **AWS Glue**는 ETL 서비스이면서 동시에 개인식별정보(PII) 탐지 및 마스킹 기능을 제공하여, 컬럼 단위와 셀 단위 모두에서 민감한 데이터를 자동으로 식별하고 제거할 수 있습니다.

이 조합은 GDPR, HIPAA 등 데이터 보호 규정을 준수해야 하는 머신러닝 프로젝트에서 핵심적인 역할을 합니다. KMS 키를 지정하지 않으면 SageMaker는 임시 키로 암호화 후 즉시 폐기하지만, 컴플라이언스 요구사항을 만족하려면 고객 관리 KMS 키를 사용해야 합니다.

### 아키텍처 플로우

```
원본 데이터 (이름, 카드번호 포함)
    ↓
S3 버킷 (KMS 암호화)
    ↓
AWS Glue (PII 탐지 및 마스킹)
    ↓
정제된 데이터셋
    ↓
SageMaker 노트북 (KMS 암호화 볼륨)
    ↓
ML 모델 학습
```

### Trade-offs 고려사항

**AWS KMS 장점**:
- 정부 및 산업 표준 준수 (FIPS 140-2 Level 2)
- 키 사용 내역 CloudTrail로 완전 추적 가능
- 세밀한 액세스 제어 정책 설정
- S3과 SageMaker 모두 네이티브 지원

**AWS KMS 단점**:
- 추가 비용 발생 (키 사용량 기반)
- 키 관리 복잡성 증가
- 암호화/복호화로 인한 약간의 성능 오버헤드

**AWS Glue 장점**:
- 자동 PII 탐지 (이름, 전화번호, 신용카드 등)
- 다양한 마스킹 옵션 (완전 삭제, 부분 마스킹, 해싱)
- 대용량 데이터 병렬 처리
- 다른 AWS 서비스와의 원활한 통합

**클라이언트 측 암호화 단점**:
- 키 관리 부담이 고객에게 전가
- SageMaker 서비스와의 통합 복잡성
- 데이터 처리 시 복호화 단계 필요

## 🔍 주요개념

### 암호화 방식 비교

- **KMS 서버측 암호화**: AWS가 키를 관리하며 서비스 레벨에서 투명한 암호화/복호화
- **클라이언트측 암호화**: 사용자가 직접 암호화하여 전송, 키 관리 책임 전가
- **IAM 정책**: 액세스 제어는 가능하지만 데이터 자체를 암호화하지는 않음

### 실전 적용

- **금융 서비스**: 신용카드 정보와 개인정보가 포함된 사기 탐지 모델 개발
- **헬스케어**: 환자 정보를 포함한 의료 진단 AI 모델 학습
- **전자상거래**: 고객 PII가 포함된 추천 시스템 및 고객 이탈 예측 모델

## 📝 관련 문제

**Question:** An IT Security Team will upload datasets containing confidential information, such as names and credit card numbers, to AWS. A Machine Learning Specialist will utilize these datasets to build a machine learning model. Due to compliance reasons, the datasets and the sensitive information within them should be encrypted and redacted. How can the Security Team achieve these requirements?

**Options:**

- A) Encrypt the data with a client-side encryption key before sending them on a SageMaker notebook instance. Hide the sensitive information with the SageMaker BlazingText algorithm.
- B) Encrypt the data with a client-side encryption key before sending them on a SageMaker notebook instance. Reduce the length of sensitive information with the SageMaker Principal Component Analysis (PCA) algorithm.
- C) Encrypt the data stored on Amazon S3 with an IAM Policy and obfuscate the credit card numbers using the Amazon SageMaker DeepAR algorithm.
- D) Encrypt the data stored on Amazon S3 with AWS KMS and enable KMS encryption when creating a SageMaker notebook instance. Use AWS Glue to redact the sensitive information within the dataset.

**정답: D) AWS KMS로 S3 데이터 암호화 및 AWS Glue로 민감정보 마스킹**

💡 추가 설명:

- **옵션 A (BlazingText)** - BlazingText는 자연어 처리 및 텍스트 분류용 알고리즘으로 민감정보 은닉 용도가 아님
- **옵션 B (PCA)** - 주성분 분석은 차원 축소 기법으로 특정 민감정보의 길이를 줄이는 용도가 아님
- **옵션 C (IAM + DeepAR)** - IAM 정책은 데이터 암호화가 아닌 접근 제어이며, DeepAR은 예측 알고리즘으로 데이터 난독화 불가능