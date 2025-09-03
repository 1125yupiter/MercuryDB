---

title: preprocessing-sagemaker-spark-xgboost
created: 2025-08-18 
modified: 2025-08-18 
tags:
- service/sagemaker-spark
- problem/customer-churn
- constraint/large-scale
- technique/preprocessing
- algorithm/xgboost
aliases: ["spark-sagemaker", "xgboost-preprocessing", "large-scale-ml"]

---

# SageMaker Spark를 활용한 대규모 데이터 전처리 및 XGBoost 훈련

## 🎯 핵심 포인트

수백만 행의 대규모 데이터셋에서 고객 이탈 예측을 위한 XGBoost 모델을 훈련하는 경우, SageMaker Spark 라이브러리를 사용하여 별도의 EMR 클러스터 관리 없이 효율적인 데이터 전처리와 모델 훈련 통합이 가능하다.

## 📝 설명

### SageMaker Spark가 대규모 ML 파이프라인에 적합한 이유

SageMaker Spark 라이브러리는 Apache Spark의 분산 처리 능력과 SageMaker의 관리형 ML 서비스를 완벽하게 통합합니다. 이를 통해 개발팀은 복잡한 인프라 관리 없이 대규모 데이터 전처리와 모델 훈련을 단일 워크플로우에서 처리할 수 있습니다. EMR 클러스터의 설정, 확장, 관리에 대한 부담을 제거하면서도 Spark의 모든 기능을 활용할 수 있어 운영 복잡성을 대폭 줄입니다.

### 아키텍처 플로우

```
원시 데이터 (S3)
    ↓
SageMaker Notebook/Studio
    ↓
SageMaker Spark Library
    ↓ (대규모 데이터 전처리)
전처리된 데이터
    ↓
XGBoostSageMakerEstimator
    ↓ (분산 훈련)
훈련된 모델
    ↓
SageMaker 엔드포인트 (실시간 추론)
```

### Trade-offs 고려사항

**SageMaker Spark 장점**:
- EMR 클러스터 프로비저닝 및 관리 불필요
- SageMaker 생태계와의 완벽한 통합
- 자동 스케일링 및 리소스 최적화
- 단일 환경에서의 전체 ML 워크플로우 관리
- 운영 오버헤드 최소화

**SageMaker Spark 단점**:
- Spark 설정의 세부 제어 제한
- EMR 기반 솔루션 대비 커스터마이징 옵션 제한

**EMR + S3 중간 저장 장점**:
- Spark 클러스터에 대한 완전한 제어
- 기존 EMR 기반 인프라 활용 가능

**EMR + S3 중간 저장 단점**:
- 불필요한 데이터 저장 단계로 인한 지연시간 증가
- EMR 클러스터 관리 복잡성
- 비용 최적화의 어려움

## 🔍 주요개념

### 통합 방식 비교

- **SageMaker Spark**: SageMaker 내부에서 Spark 작업을 직접 실행하는 완전 통합 방식
- **Sparkmagic/Livy**: SageMaker 노트북에서 별도 EMR 클러스터에 접근하는 하이브리드 방식
- **EMR + S3**: EMR에서 전처리 후 S3를 통해 SageMaker로 데이터를 전달하는 분리 방식

### 실전 적용

- 수백만 행의 고객 거래 데이터에서 이탈 패턴 분석 및 특성 엔지니어링
- 실시간 추천 시스템을 위한 대규모 사용자 행동 데이터 전처리
- 제조업체의 센서 데이터를 활용한 예측 정비 모델 구축

## 📝 관련 문제

**Question:** A data science team needs to process millions of customer records for churn prediction using Apache Spark for preprocessing and XGBoost for training. They want the simplest architecture that minimizes infrastructure management while maintaining scalability. Which approach should they choose?

**Options:**

- A) Configure Livy in SageMaker, preprocess data on EMR Spark via SageMaker notebook, train XGBoost PySpark Estimator
- B) Use SageMaker Spark to preprocess data, train with XGBoostSageMakerEstimator, and host on SageMaker
- C) Configure Sparkmagic in SageMaker, preprocess data on EMR Spark via SageMaker notebook, train with SageMaker XGBoost
- D) Preprocess data on EMR Spark, save in S3, use SageMaker to train XGBoost, and host on SageMaker
- E) Set up standalone Spark cluster, preprocess data, transfer to SageMaker for training

**정답: B) Use SageMaker Spark to preprocess data, train with XGBoostSageMakerEstimator**

💡 추가 설명:

- **Option A (Livy + EMR)** - EMR 클러스터 관리가 필요하여 운영 복잡성이 증가
- **Option C (Sparkmagic + EMR)** - EMR 프로비저닝이 필요하고 설정이 복잡함  
- **Option D (EMR + S3 중간저장)** - 불필요한 데이터 저장 단계로 지연시간 증가
- **Option E (독립 Spark 클러스터)** - 인프라 관리 부담이 가장 큼