---

title: regression-sagemaker-linearlearner-forecast
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/regression
- service/sagemaker
- algorithm/linear-learner
- constraint/cost-effective
- usecase/air-quality-forecast
aliases: ["regression-forecast", "linear-learner-regression", "NO2-prediction"]

---

# SageMaker Linear Learner를 활용한 대기질 농도 회귀 예측

## 🎯 핵심 포인트

연속적인 수치값(NO2 농도)을 예측해야 하는 경우 SageMaker에서, Linear Learner 알고리즘의 predictor_type을 regressor로 설정하여 비용 효율적인 회귀 예측을 수행할 수 있다.

## 📝 설명

### Linear Learner가 회귀 예측에 적합한 이유

Linear Learner는 분류와 회귀 작업을 모두 지원하는 간단하면서도 강력한 알고리즘입니다. 회귀 설정에서는 특성 간의 선형 관계를 활용하여 연속적인 수치값을 예측하며, 가중치 요소를 통해 어떤 특성이 주요 예측 변수인지 파악할 수 있습니다.

**회귀 예측에서의 핵심 특징:**
- predictor_type을 'regressor'로 설정하여 연속적인 수치값 예측
- 대용량 데이터셋(40,000+ 관측치)에서도 효율적 처리
- 시계열 예측에서 Pure time-series와 Hybrid 접근법 모두 지원
- 추론 비용이 저렴하여 실시간 예측 서비스에 적합

### 아키텍처 플로우

```
Historical Weather Data (5년, 40k observations)
           ↓
    Feature Engineering
    (기상 조건, 과거 NO2 농도)
           ↓
    SageMaker Linear Learner
    (predictor_type: regressor)
           ↓
    Model Training & Validation
           ↓
    NO2 농도 예측 (다음 7일)
           ↓
    Low-cost Inference Endpoint
```

### Trade-offs 고려사항

**Linear Learner (Regressor) 장점**:
- 빠른 훈련 속도와 저렴한 추론 비용
- 대용량 데이터셋 효율적 처리
- 선형 관계가 명확한 문제에서 높은 성능
- 특성 중요도 파악 가능 (가중치 분석)

**Linear Learner (Regressor) 단점**:
- 복잡한 비선형 패턴 포착 한계
- 특성 간 상호작용 모델링 제한적
- 이상치에 민감할 수 있음

**k-Nearest Neighbors (kNN) 장점**:
- 비선형 패턴 학습 가능
- 지역적 패턴 포착에 우수

**k-Nearest Neighbors (kNN) 단점**:
- Lazy learner로 모든 훈련 데이터 메모리 보관 필요
- 추론 시 O(nd) 연산 복잡도로 비용 급증
- 40k 관측치에서 실시간 추론 비현실적

## 🔍 주요개념

### 예측 유형 비교

- **Regression (회귀)**: 연속적인 수치값 예측 (NO2 농도: 15.2 μg/m³)
- **Classification (분류)**: 카테고리 예측 (공기질 등급: 좋음/보통/나쁨)
- **Multiclass Classification**: 3개 이상 클래스 분류

### 실전 적용

- **대기질 모니터링**: 기상 조건 기반 오염물질 농도 예측
- **에너지 수요 예측**: 과거 사용량과 날씨 데이터로 전력 수요 예측
- **재고 수요 예측**: 계절성과 프로모션 효과를 고려한 판매량 예측

## 📝 관련 문제

**Question:** A Machine Learning Specialist is planning to train a model using Amazon SageMaker to determine the impact of weather conditions on the air quality of a city. The ML Specialist wants to forecast the NO2 concentration for the next 7 days. She will be using 5 years' worth of data that has roughly about 40,000 observations. As much as possible, the model must provide cheap inference costs. Which algorithm should be used to build the model?

**Options:**

- A) Use the k-Nearest-Neighbors (kNN) algorithm and set the predictor_type hyperparameter to regressor
- B) Use the Linear Learner algorithm and set the predictor_type hyperparameter to multiclass_classifier
- C) Use the Random Cut Forest (RCF) algorithm and set the eval_metrics hyperparameter to accuracy
- D) Use the Linear Learner algorithm and set the predictor_type hyperparameter to regressor

**정답: D) Use the Linear Learner algorithm and set the predictor_type hyperparameter to regressor**

💡 추가 설명:

- **Option A (kNN regressor)** - 기술적으로 회귀 가능하지만 추론 비용이 매우 높음. 40k 데이터에서 각 예측마다 O(nd) 연산 필요로 비현실적
- **Option B (Linear Learner multiclass)** - 분류 작업용 설정으로 연속적 NO2 농도값 예측 불가능
- **Option C (Random Cut Forest)** - 이상 탐지 전용 알고리즘으로 회귀 예측 작업에 부적합