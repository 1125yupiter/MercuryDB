---

title: sagemaker-xgboost-framework-custom
created: 2025-09-01
modified: 2025-09-01
tags:
- service/sagemaker
- algorithm/xgboost
- method/framework-mode
- technique/custom-preprocessing
- deployment/training
aliases: ["xgboost-framework", "sagemaker-xgb", "framework-mode"]

---

# SageMaker XGBoost Framework Mode와 Built-in Algorithm 비교

## 🎯 핵심 포인트

커스텀 전처리나 특성 엔지니어링이 필요한 경우 SageMaker에서, XGBoost Framework Mode를 사용하여 유연한 모델 개발을 할 수 있다.

## 📝 설명

### SageMaker XGBoost가 커스텀 로직에 적합한 이유

SageMaker는 XGBoost를 두 가지 방식으로 지원한다. Built-in Algorithm은 하이퍼파라미터만 설정하면 되는 간단한 방식이지만, Framework Mode는 사전 구성된 XGBoost 컨테이너를 사용하면서도 커스텀 훈련 스크립트를 통해 전체 훈련 프로세스를 제어할 수 있다.

Framework Mode에서는 `entry_point`로 지정한 Python 스크립트가 실행되며, 이 스크립트 내에서 데이터 전처리, 특성 엔지니어링, 모델 검증, 그리고 XGBoost 모델 훈련까지 모든 과정을 커스터마이징할 수 있다.

### 아키텍처 플로우

```
데이터 입력 → SageMaker XGBoost Container → Custom train.py Script
                                          ↓
커스텀 전처리 → 특성 엔지니어링 → XGBoost 훈련 → 모델 저장
                                          ↓
                              SageMaker Model Artifacts
```

### Trade-offs 고려사항

**Framework Mode 장점**:
- 커스텀 전처리 및 특성 엔지니어링 가능
- 복잡한 데이터 파이프라인 구현
- 모델 검증 로직 커스터마이징
- 최적화된 XGBoost 컨테이너 환경 제공

**Framework Mode 단점**:
- 추가 코드 작성 필요
- Built-in 대비 설정 복잡도 증가
- 디버깅 시 커스텀 스크립트 고려 필요

**Built-in Algorithm 장점**:
- 설정 간단 (하이퍼파라미터만)
- 즉시 사용 가능
- 검증된 안정성

**Built-in Algorithm 단점**:
- 기본 데이터 처리만 가능
- 커스텀 로직 구현 불가
- 전처리 파이프라인 제한

## 🔍 주요개념

### 구현 방식 비교

- **Built-in Algorithm**: `sagemaker.estimator.Estimator`로 XGBoost 컨테이너 이미지 직접 사용
- **Framework Mode**: `sagemaker.xgboost.XGBoost` 클래스로 커스텀 스크립트 실행

### 실전 적용

- 복잡한 특성 엔지니어링이 필요한 금융 리스크 모델링
- 다중 데이터 소스 통합이 필요한 고객 세그멘테이션
- 시계열 특성 생성이 필요한 수요 예측 모델

## 📝 관련 문제

**Question:** A data scientist needs to implement custom feature engineering and data preprocessing before training an XGBoost model in SageMaker. The preprocessing involves complex business logic that cannot be handled by standard transformations. Which approach should be used?

**Options:**

- A) Use SageMaker XGBoost built-in algorithm with hyperparameter tuning
- B) Use SageMaker XGBoost Framework Mode with custom training script
- C) Use SageMaker Processing Job followed by built-in XGBoost
- D) Use SageMaker Pipelines with built-in XGBoost algorithm
- E) Use Amazon Comprehend for preprocessing then XGBoost

**정답: B) SageMaker XGBoost Framework Mode with custom training script**

💡 추가 설명:

- **A) Built-in algorithm** - 커스텀 전처리 로직 구현 불가능
- **C) Processing Job** - 별도 작업 필요하여 복잡도 증가
- **D) SageMaker Pipelines** - 파이프라인 오케스트레이션 도구로 커스텀 로직 해결 안됨
- **E) Amazon Comprehend** - 자연어 처리 서비스로 일반적 전처리에 부적합

---