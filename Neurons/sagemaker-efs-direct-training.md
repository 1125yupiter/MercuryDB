---

title: sagemaker-efs-direct-training
created: 2025-08-26
modified: 2025-08-26
tags:
- service/sagemaker
- storage/efs
- constraint/minimal-steps
- deployment/cost-effective
- usecase/image-classification
aliases: ["sagemaker-efs", "efs-training", "direct-mounting"]

---

# SageMaker EFS 직접 연결을 통한 효율적인 모델 훈련

## 🎯 핵심 포인트

비용과 시간 제약이 있는 상황에서 EFS 데이터셋을 활용한 이미지 분류 모델 훈련의 경우, SageMaker에서 EFS 파일 시스템을 직접 데이터 소스로 사용할 수 있다.

## 📝 설명

### SageMaker가 EFS 직접 연결에 적합한 이유

SageMaker는 EFS(Elastic File System)를 네이티브로 지원하여 훈련 작업 시 직접 마운트가 가능합니다. 이는 대용량 이미지 데이터셋의 경우 특히 유용하며, 데이터 이동 시간과 추가 스토리지 비용을 제거할 수 있습니다. EFS는 다수의 훈련 인스턴스에서 동시 접근이 가능하고, NFS 프로토콜을 통해 안정적인 파일 시스템 성능을 제공합니다.

### 아키텍처 플로우

```
EFS File System (Images) 
    ↓ (Direct Mount)
SageMaker Training Job
    ↓
Model Artifacts → S3
```

### Trade-offs 고려사항

**EFS 직접 연결 장점**:
- 데이터 이동 시간 제거 (특히 대용량 이미지 데이터)
- 추가 스토리지 비용 없음
- 단일 단계 설정으로 최소 통합 노력
- 다중 인스턴스 동시 접근 지원

**EFS 직접 연결 단점**:
- S3 대비 상대적으로 낮은 처리량
- 네트워크 지연 가능성

**S3 활용 장점**:
- 높은 처리량과 확장성
- SageMaker와 최적화된 통합

**S3 활용 단점**:
- 데이터 복사 시간과 비용 발생
- 추가 스토리지 공간 필요
- 복잡한 다단계 프로세스

## 🔍 주요개념

### 스토리지 옵션 비교

- **EFS**: 네트워크 파일 시스템으로 다중 인스턴스 공유 가능, 직접 마운트 지원
- **FSx for Lustre**: 고성능 파일 시스템이지만 추가 설정과 비용 발생
- **S3**: 객체 스토리지로 최고 처리량 제공하지만 데이터 이동 필요

### 실전 적용

- 대용량 이미지/비디오 데이터셋을 활용한 컴퓨터 비전 모델 훈련
- 시간과 비용 제약이 있는 프로토타입 개발 환경
- 기존 EFS에 저장된 데이터를 즉시 활용해야 하는 경우

## 📝 관련 문제

**Question:** A data scientist has an Amazon EFS dataset of machine component photos. The data scientist must use Amazon SageMaker to design and train a machine learning model for image classification. Due to budget and time constraints, management expects the data scientist to design and train a model with the fewest possible steps and minimal integration effort. How should the data scientist meet these requirements?

**Options:**

- A) Mount the EFS file system to a SageMaker notebook and run a script that copies the data to an Amazon FSx for Lustre file system. Run the SageMaker training job with the FSx for Lustre file system as the data source.
- B) Launch a transient Amazon EMR cluster. Configure steps to mount the EFS file system and copy the data to an Amazon S3 bucket using S3DistCp. Run the SageMaker training job with Amazon S3 as the data source.
- C) Mount the EFS file system to an Amazon EC2 instance and use the AWS CLI to copy the data to an Amazon S3 bucket. Run the SageMaker training job with Amazon S3 as the data source.
- D) Run a SageMaker training job with an EFS file system as the data source.

**정답: D) Run a SageMaker training job with an EFS file system as the data source**

💡 추가 설명:

- **Option A** - FSx for Lustre로의 데이터 복사는 추가 비용과 복잡성을 증가시킴
- **Option B** - EMR 클러스터 생성과 S3DistCp를 통한 복사는 가장 복잡한 다단계 프로세스
- **Option C** - EC2 인스턴스를 통한 수동 데이터 복사는 시간과 비용이 많이 소요됨