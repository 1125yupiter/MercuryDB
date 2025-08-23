---

title: ml-data-preprocessing-normalization
created: 2025-08-23
modified: 2025-08-23
tags:
- problem/feature-scaling
- technique/normalization
- constraint/magnitude-variation
- method/supervised-learning
- usecase/employment-forecast
aliases: ["normalization", "standardization", "feature-scaling"]

---

# ML 데이터 전처리 - 정규화를 통한 특성 스케일링

## 🎯 핵심 포인트

입력 특성들의 크기(amplitude)가 크게 다른 경우 모델 학습에서 정규화를 통해 각 특성의 평균을 0, 분산을 1로 만들어 크기에 의한 편향을 제거할 수 있다.

## 📝 설명

### 정규화가 특성 스케일링에 적합한 이유

머신러닝 모델에서 입력 특성들의 크기가 크게 다를 때, 큰 값을 가진 특성이 모델의 학습 과정을 지배하게 되는 문제가 발생한다. 예를 들어 고용률 예측에서 GDP(조 단위)와 인플레이션율(%) 같은 서로 다른 스케일의 변수들이 함께 사용될 때, GDP의 큰 수치가 모델에 과도한 영향을 미칠 수 있다.

정규화(Normalization) 또는 표준화(Standardization)는 각 특성을 평균 0, 분산 1인 표준 정규분포로 변환하여 모든 특성이 동일한 스케일에서 기여할 수 있도록 한다. 이는 특히 거리 기반 알고리즘(KNN, SVM), 신경망, 선형 회귀 등에서 필수적이다.

### 아키텍처 플로우

```
Raw Data (다양한 스케일) 
    ↓
Feature Analysis (평균, 분산 계산)
    ↓
Standardization 적용 (X - μ) / σ
    ↓
Normalized Features (평균=0, 분산=1)
    ↓
Model Training (균등한 특성 기여도)
    ↓
Employment Rate Prediction
```

### Trade-offs 고려사항

**정규화(Standardization) 장점**:

- 모든 특성이 동일한 스케일에서 기여
- 그래디언트 기반 최적화 알고리즘의 수렴 속도 향상
- 특성 간 상대적 중요도 비교 가능
- 대부분의 ML 알고리즘에서 성능 향상

**정규화(Standardization) 단점**:

- 원본 데이터의 해석이 어려워짐
- 이상치에 민감할 수 있음
- 추가 계산 비용 발생

**양자화 비닝(Quantile Binning) 장점**:

- 이상치에 강건함
- 비선형 관계를 캡처할 수 있음

**양자화 비닝(Quantile Binning) 단점**:

- 연속형 데이터를 범주형으로 변환하여 정보 손실
- 비닝 경계 설정의 어려움
- 고용률 예측 같은 회귀 문제에서 부적합

## 🔍 주요개념

### 데이터 전처리 기법 비교

- **표준화(Standardization)**: 평균 0, 분산 1로 변환하여 정규분포 근사
- **정규화(Min-Max Normalization)**: 0~1 범위로 스케일링
- **양자화 비닝**: 연속형 데이터를 동일한 크기의 구간으로 분할
- **직교 희소 바이그램(OSB)**: 텍스트 처리를 위한 n-gram 기반 특성 생성

### 실전 적용

- 경제 지표 기반 고용률 예측 (GDP, 인플레이션, 금리 등 다양한 스케일)
- 주식 가격 예측 (거래량, 가격, 비율 등)
- 의료 진단 (혈압, 콜레스테롤, 나이 등 서로 다른 단위)

## 📝 관련 문제

**Question:** A Machine Learning Specialist is developing a model to forecast future employment rates using a variety of economic variables. While analyzing the data, the Specialist observes that the amplitude of the input attributes varies significantly. The Specialist does not want the model to be dominated by factors of greater size. What is the Specialist's role in preparing data for model training?

**Options:**

- A) Apply quantile binning to group the data into categorical bins to keep any relationships in the data by replacing the magnitude with distribution
- B) Apply the Cartesian product transformation to create new combinations of fields that are independent of the magnitude
- C) Apply normalization to ensure each field will have a mean of 0 and a variance of 1 to remove any significant magnitude
- D) Apply the orthogonal sparse bigram (OSB) transformation to apply a fixed-size sliding window to generate new features of a similar magnitude

**정답: C) Apply normalization to ensure each field will have a mean of 0 and a variance of 1 to remove any significant magnitude**

💡 추가 설명:

- **A) Quantile Binning** - 연속형 데이터를 범주형으로 변환하여 회귀 문제에 부적합하고 정보 손실 발생
- **B) Cartesian Product** - 특성 간 조합을 생성하지만 크기 문제를 해결하지 못하고 차원만 증가
- **D) OSB Transformation** - 주로 텍스트 처리용 기법으로 수치형 경제 변수에 부적합