---

title: sagemaker-spot-training-checkpointing
created: 2025-08-27
modified: 2025-08-27
tags:
- service/sagemaker
- constraint/cost-sensitive
- technique/checkpointing
- deployment/spot-instances
- usecase/large-scale-training
aliases: ["spot-training", "managed-spot", "checkpoint-training"]

---

# SageMaker Managed Spot Training - 대규모 모델 훈련의 비용 효율적 솔루션

## 🎯 핵심 포인트

대규모 모델 훈련에서 비용을 최소화하면서 작업 손실을 방지해야 하는 경우, SageMaker Managed Spot Training의 체크포인팅 기능을 통해 최대 90% 비용 절약과 훈련 연속성을 동시에 확보할 수 있다.

## 📝 설명

### SageMaker Managed Spot Training이 대규모 모델 훈련에 적합한 이유

SageMaker Managed Spot Training은 EC2 Spot Instance의 저렴한 비용과 자동 체크포인팅 메커니즘을 결합하여 대규모 ML 모델 훈련의 비용 효율성과 안정성을 모두 보장합니다. 

**핵심 동작 방식:**
- 훈련 중 모델 가중치, 옵티마이저 상태, 에포크 정보를 S3에 주기적으로 자동 저장
- Spot Instance 중단 시 SageMaker가 자동으로 감지하고 새로운 인스턴스 할당
- 마지막 체크포인트에서 훈련을 자동 재개 (처음부터 재시작하지 않음)

### 아키텍처 플로우

```
훈련 시작 → 주기적 체크포인트 저장(S3) → Spot 인스턴스 중단 감지
    ↓
새 Spot 인스턴스 할당 → 체크포인트 로드 → 중단된 지점부터 훈련 재개
    ↓
훈련 완료 → 최종 모델 S3 저장
```

### Trade-offs 고려사항

**SageMaker Managed Spot Training 장점**:
- 최대 90% 비용 절약 (On-Demand 대비)
- 자동 체크포인팅으로 진행 상황 보존
- 완전 관리형 서비스로 운영 오버헤드 최소화
- ML 워크로드에 최적화된 환경 (GPU 지원, 분산 훈련)
- 자동 재시작 및 복구 메커니즘

**SageMaker Managed Spot Training 단점**:
- Spot Instance 가용성에 따른 잠재적 지연
- 체크포인팅 오버헤드 (저장 시간)
- 중단 가능성으로 인한 예측 불가능한 완료 시간

**AWS Batch + Spot Instances 장점**:
- 일반적인 배치 작업에 적합
- 유연한 컴퓨팅 환경 설정

**AWS Batch + Spot Instances 단점**:
- ML 특화 기능 부족
- 수동 체크포인팅 구현 필요
- 높은 운영 복잡성

**EC2 Spot Instance 직접 사용 장점**:
- 최대 제어권
- 커스터마이징 가능

**EC2 Spot Instance 직접 사용 단점**:
- 높은 운영 오버헤드
- 수동 중단 처리 및 스냅샷 관리
- 인프라 관리 부담

**AWS Lambda 장점**:
- 서버리스 아키텍처
- 자동 스케일링

**AWS Lambda 단점**:
- 실행 시간 제한 (최대 15분)
- 메모리 제한 (최대 10GB)
- GPU 지원 없음

## 🔍 주요개념

### 체크포인팅 vs 스냅샷

- **체크포인팅**: 훈련 중 모델 상태를 주기적으로 저장하여 중단 시 재개 가능한 지점 생성
- **스냅샷**: 전체 시스템 상태의 일회성 백업 (더 무겁고 복구 시간 오래 걸림)

### 실전 적용

- **대규모 이미지 분류**: 10,000개 클래스 CNN 모델 훈련 시 에포크마다 체크포인트 저장
- **NLP 모델 파인튜닝**: BERT, GPT 등 대형 언어모델의 도메인 특화 학습
- **시계열 예측 모델**: DeepAR 등 복잡한 시계열 모델의 장기간 훈련

## 📝 관련 문제

**Question:** An ecommerce company wants to train a large image classification model with 10,000 classes. The company runs multiple model training iterations and needs to minimize operational overhead and cost. The company also needs to avoid loss of work and model retraining. Which solution will meet these requirements?

**Options:**

- A) Create the training jobs as AWS Batch jobs that use Amazon EC2 Spot Instances in a managed compute environment
- B) Use Amazon EC2 Spot Instances to run the training jobs. Use a Spot Instance interruption notice to save a snapshot of the model to Amazon S3 before an instance is terminated  
- C) Use AWS Lambda to run the training jobs. Save model weights to Amazon S3
- D) Use managed spot training in Amazon SageMaker. Launch the training jobs with checkpointing enabled

**정답: D) Use managed spot training in Amazon SageMaker. Launch the training jobs with checkpointing enabled**

💡 추가 설명:

- **옵션 A** - 체크포인팅 메커니즘이 없어 Spot Instance 중단 시 진행 상황 손실 위험
- **옵션 B** - 수동 중단 처리로 인한 높은 운영 오버헤드와 복잡한 스냅샷 관리 필요  
- **옵션 C** - Lambda의 15분 실행 제한과 10GB 메모리 제한으로 대규모 모델 훈련 불가능
- **옵션 D** - 자동 체크포인팅으로 작업 보호, 비용 최적화, 운영 오버헤드 최소화 모두 만족

---