---

title: regression-sagemaker-regularization-underfitting
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/regression
- service/sagemaker
- technique/regularization
- constraint/underfitting
- usecase/energy-prediction
aliases: ["L1-regularization", "underfitting-solution", "lasso-ridge"]

---

# 회귀 모델에서 L1 정규화로 인한 언더피팅 해결 방안

## 🎯 핵심 포인트

L1 정규화가 너무 강하게 적용되어 모델이 언더피팅된 경우, L1 정규화 파라미터를 줄이거나 L2 정규화로 전환하여 예측 성능을 개선할 수 있다.

## 📝 설명

### L1 정규화가 스마트홈 에너지 예측에서 언더피팅을 야기하는 이유

L1 정규화(Lasso)는 특성 선택 효과를 통해 모델의 해석성을 향상시키지만, 너무 강하게 적용되면 중요한 특성들까지 제거하여 언더피팅을 발생시킵니다. 스마트홈 환경에서는 온도, 습도, 조명, 기기 사용 패턴 등 다양한 환경 및 사용 특성이 모두 에너지 소비에 영향을 미치는데, 과도한 L1 정규화로 인해 이러한 중요 특성들의 계수가 0이 되어 버립니다.

### 정규화 방식별 특징 비교

**L1 정규화 (Lasso)**
- 계수의 절댓값에 페널티 부여
- 불필요한 특성의 계수를 정확히 0으로 만듦
- 자동 특성 선택 효과
- 희소한(sparse) 모델 생성

**L2 정규화 (Ridge)**
- 계수의 제곱에 페널티 부여
- 계수를 0에 가깝게 만들지만 완전히 제거하지는 않음
- 모든 특성을 일정 수준 유지
- 다중공선성 문제 완화

### 아키텍처 플로우

```
데이터 수집 (환경/사용 특성)
         ↓
특성 엔지니어링 (AWS Glue)
         ↓
모델 학습 (SageMaker Linear Learner)
         ↓
정규화 파라미터 조정
    ↙        ↘
L1 감소    L2 전환
    ↘        ↙
성능 향상된 예측 모델
```

### Trade-offs 고려사항

**L1 정규화 파라미터 감소 장점**:
- 더 많은 특성 활용 가능
- 언더피팅 문제 직접적 해결
- 여전히 일정 수준의 특성 선택 효과 유지
- SageMaker 내장 최적화 도구 활용 가능

**L1 정규화 파라미터 감소 단점**:
- 과도하게 줄이면 오버피팅 위험
- 모델 해석성 다소 감소
- 최적 파라미터 찾기 위한 추가 튜닝 필요

**L2 정규화 전환 장점**:
- 모든 특성의 기여도 유지
- 복잡한 데이터셋에서 일반화 성능 우수
- 다중공선성 문제 완화
- 안정적인 학습 과정

**L2 정규화 전환 단점**:
- 자동 특성 선택 효과 없음
- 불필요한 특성까지 포함하여 해석성 저하
- 모델 복잡도 증가

## 🔍 주요개념

### 정규화 방식별 수학적 특성

- **L1 정규화**: λ∑|βᵢ| - 절댓값 기반 페널티로 희소성 촉진
- **L2 정규화**: λ∑βᵢ² - 제곱 기반 페널티로 계수 크기 제한

### 실전 적용 시나리오

- **스마트홈 에너지 관리**: 다양한 환경 센서 데이터 활용한 실시간 소비 예측
- **IoT 디바이스 최적화**: 기기별 사용 패턴 학습을 통한 효율적 에너지 배분
- **건물 관리 시스템**: 계절별, 시간대별 에너지 수요 예측 및 비용 최적화

## 📝 관련 문제

**Question:** An AI startup is developing a regression model to predict energy consumption in smart homes based on numerous environmental and usage features. To enhance model simplicity and interpretability, L1 regularization was incorporated. However, preliminary testing indicates the model is underfitting, leading to poor predictions. Which two adjustments could potentially improve the model's accuracy in this scenario?

**Options:**

- A) Increase the data sampling rate to capture more granular environmental and usage features
- B) Switch to L2 regularization in Amazon SageMaker's Linear Learner for a different penalty approach
- C) Reduce the L1 regularization parameter using Amazon SageMaker's built-in optimization
- D) Amplify the L1 regularization term with Amazon SageMaker's algorithm tuning
- E) Employ AWS Glue DataBrew to trim less significant features before modeling

**정답: B, C) L2 정규화 전환 및 L1 정규화 파라미터 감소**

💡 추가 설명:

- **Option A** - 데이터 세분화는 복잡도만 증가시키고 정규화로 인한 언더피팅 근본 원인을 해결하지 못함
- **Option D** - L1 정규화 강화는 언더피팅을 더욱 악화시켜 예측 성능을 저하시킴
- **Option E** - 특성 제거는 정규화 파라미터 조정과 무관하며 언더피팅 문제 해결에 직접적 도움 안 됨

---