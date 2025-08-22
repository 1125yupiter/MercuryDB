---

title: sagemaker-processing-iam-permissions
created: 2025-08-22
modified: 2025-08-22
tags:
- service/sagemaker
- constraint/secure-data
- deployment/processing-jobs
- permissions/iam-roles
- storage/s3-kms
aliases: ["sagemaker-iam", "processing-permissions", "ml-security"]

---

# SageMaker Processing을 통한 안전한 데이터 전처리

## 🎯 핵심 포인트

KMS로 암호화된 S3 데이터를 SageMaker Processing으로 전처리하는 경우 노트북 인스턴스에서, 모든 필요한 권한을 하나의 IAM 역할에 통합하여 관리할 수 있다.

## 📝 설명

### SageMaker Processing이 안전한 데이터 전처리에 적합한 이유

SageMaker Processing은 ML 파이프라인에서 데이터 전처리 작업을 독립적으로 실행할 수 있는 완전 관리형 서비스입니다. KMS 암호화와 ECR 컨테이너 이미지를 활용하여 보안이 강화된 환경에서 대용량 데이터 처리가 가능합니다. 노트북 인스턴스에서 트리거되어 확장 가능한 컴퓨팅 리소스를 활용하며, 처리 완료 후 자동으로 리소스가 해제되어 비용 효율적입니다.

### 아키텍처 플로우

```
SageMaker Notebook Instance (IAM Role 부착)
       ↓
Processing Job 생성 및 실행
       ↓
ECR에서 컨테이너 이미지 Pull
       ↓
KMS CMK로 S3 데이터 복호화 및 다운로드
       ↓
컨테이너에서 전처리 코드 실행
       ↓
처리된 데이터를 KMS CMK로 암호화하여 S3 업로드
       ↓
Processing Job 완료 및 리소스 정리
```

### Trade-offs 고려사항

**단일 IAM 역할 방식 (A번) 장점**:
- 권한 관리 단순화로 운영 복잡성 감소
- 최소 권한 원칙 적용 용이
- 보안 감사 및 모니터링 간소화
- 설정 오류 가능성 최소화

**단일 IAM 역할 방식 단점**:
- 역할에 다양한 권한이 집중될 수 있음
- 세밀한 권한 분리가 어려울 수 있음

**별도 IAM 역할 방식 (B번) 장점**:
- 노트북과 Processing 작업 간 권한 분리
- 보다 세분화된 액세스 제어

**별도 IAM 역할 방식 단점**:
- 권한 관리 복잡성 증가
- 역할 간 신뢰 관계 설정 필요
- 설정 오류 및 디버깅 어려움 증가

## 🔍 주요개념

### IAM 권한 설계 패턴 비교

- **통합 권한 모델**: 하나의 역할에 모든 필요한 권한 부여 (단순함, 관리 용이)
- **분리 권한 모델**: 서비스별로 별도 역할 생성 (세밀한 제어, 복잡성 증가)

### 실전 적용

- **금융 데이터 처리**: 고도로 암호화된 고객 거래 데이터를 안전하게 전처리
- **의료 영상 분석**: HIPAA 규정 준수가 필요한 의료 이미지 데이터 처리
- **IoT 센서 데이터**: 대용량 시계열 데이터의 실시간 전처리 및 특성 추출

## 📝 관련 문제

**Question:** A business is developing a machine-learning-based predictive maintenance approach. The data is encrypted at rest using AWS KMS CMKs in a secure S3 bucket. Data preparation must be performed by a machine learning professional using SageMaker Processing jobs triggered from a SageMaker notebook. The preprocessing code is contained in an ECR container image. Which activities should the machine learning professional do to satisfy these requirements?

**Options:**

- A) Create an IAM role that has permissions to create SageMaker Processing jobs, S3 read and write access to the relevant S3 bucket, and appropriate KMS and ECR permissions. Attach the role to the SageMaker notebook instance. Create a SageMaker Processing job from the notebook.
- B) Create an IAM role that has permissions to create SageMaker Processing jobs. Attach the role to the SageMaker notebook instance. Create a SageMaker Processing job with an IAM role that has read and write permissions to the relevant S3 bucket, and appropriate KMS and ECR permissions.
- C) Create an IAM role that has permissions to create SageMaker Processing jobs and to access ECR. Attach the role to the SageMaker notebook instance. Set up both an S3 endpoint and a KMS endpoint in the default VPC. Create SageMaker Processing jobs from the notebook.
- D) Create an IAM role that has permissions to create SageMaker Processing jobs. Attach the role to the SageMaker notebook instance. Set up an S3 endpoint in the default VPC. Create SageMaker Processing jobs with the access key and secret key of the IAM user with appropriate KMS and ECR permissions.

**정답: A) 통합 IAM 역할 방식**

💡 추가 설명:

- **B번** - Processing 작업에 별도 IAM 역할을 생성하는 복잡한 구조로 권한 관리 오버헤드 증가
- **C번** - VPC 엔드포인트가 요구사항에 명시되지 않았고, S3 및 KMS 권한이 Processing 작업 역할에 누락됨
- **D번** - 액세스 키/시크릿 키 사용은 AWS 보안 모범 사례 위반, IAM 역할 기반 접근이 권장됨