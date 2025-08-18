---

title: regression-sagemaker-preprocessing
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker-linear-learner
- problem/regression
- technique/normalization
- technique/data-shuffling
- constraint/multi-group-data
aliases: ["sagemaker-preprocessing", "linear-learner-prep", "regression-preprocessing"]

---

# SageMaker Linear Learner 회귀 모델 전처리 방법

## 🎯 핵심 포인트

다양한 그룹으로 구분된 훈련 데이터를 사용하여 SageMaker Linear Learner로 회귀 모델을 훈련하는 경우, 정규화(Normalization)와 데이터 셔플링(Data Shuffling)이 최적 성능을 위해 필요하다.

## 📝 설명

### SageMaker Linear Learner가 회귀 예측에 적합한 이유

SageMaker Linear Learner는 경사하강법(Gradient Descent) 기반의 최적화 알고리즘을 사용하여 선형 및 비선형 모델을 학습합니다. 연속값 예측(소득 예측)에서는 특성 간 스케일 차이와 데이터 순서가 모델 성능에 직접적인 영향을 미치므로, 적절한 전처리가 중요합니다.

### 아키텍처 플로우

```
원본 데이터 (나이, 교육연수, 소득)
    ↓
특성 정규화 (평균 0, 표준편차 1)
    ↓
데이터 셔플링 (그룹 순서 무작위화)
    ↓
SageMaker Linear Learner 훈련
    ↓
최적화된 회귀 모델
```

### Trade-offs 고려사항

**정규화(Normalization) 장점**:
- 모든 특성이 동등하게 모델 학습에 기여
- 경사하강법 수렴 속도 향상
- 스케일 편향 방지 (나이 vs 교육연수)

**정규화 단점**:
- 원본 데이터 해석 어려움
- 추가 계산 오버헤드

**데이터 셔플링 장점**:
- 그룹별 편향 학습 방지
- 배치 간 데이터 다양성 보장
- 일반화 성능 향상

**데이터 셔플링 단점**:
- 시계열 패턴이 있을 경우 정보 손실 가능
- 재현 가능성을 위한 시드 설정 필요

**잘못된 접근법들**:
- **랜덤 노이즈 추가**: 모델 성능 저하 위험
- **소득 범위 스케일링**: Linear Learner가 자동으로 가중치 조정 가능
- **SMOTE 기법**: 분류 문제용으로 회귀에 부적합

## 🔍 주요개념

### 전처리 기법 비교

- **정규화(Normalization)**: 평균 0, 표준편차 1로 변환하여 특성 간 스케일 통일
- **표준화(Standardization)**: 정규화와 동일한 개념으로 사용됨
- **스케일링(Scaling)**: 특정 범위(예: 0-1)로 값 변환
- **셔플링(Shuffling)**: 데이터 순서 무작위화로 학습 편향 방지

### 실전 적용

- **금융 서비스**: 고객 소득 예측 모델에서 나이, 경력, 교육수준 특성 정규화
- **HR 분석**: 직원 연봉 예측 시 다양한 부서별 데이터 셔플링으로 공정한 학습
- **보험업**: 보험료 산정 모델에서 지역별, 연령대별 그룹 데이터의 균형 학습

## 📝 관련 문제

**Question:** While training a regression model with SageMaker's Linear Learner to predict individual incomes based on age and years in school, the training data encompasses various distinct groups. To ensure optimal outcomes from the model, which TWO pre-processing steps should be undertaken?

**Options:**

- A) Normalize the feature data to have a mean of zero and unit standard deviation
- B) Add some random noise to the training data
- C) Shuffle the input data  
- D) Scale the feature data to match the range of the income data
- E) Utilize Synthetic Minority Over-sampling Technique (SMOTE) to impute additional data

**정답: A) Normalize the feature data, C) Shuffle the input data**

💡 추가 설명:

- **Option B (랜덤 노이즈 추가)** - 데이터 품질을 저하시켜 모델 성능에 악영향을 미침
- **Option D (소득 범위 스케일링)** - Linear Learner는 가중치로 스케일 차이를 자동 조정하므로 불필요
- **Option E (SMOTE 기법)** - 분류 문제의 클래스 불균형 해결용으로 연속값 회귀 예측에는 부적합