---

title: classification-logistic-regression-probability
created: 2025-08-23
modified: 2025-08-23
tags:
- algorithm/logistic-regression
- problem/classification
- method/supervised
- output/probability
- function/sigmoid
aliases: ["logistic", "sigmoid", "classification-prob"]

---

# 로지스틱 회귀를 활용한 확률 기반 분류

## 🎯 핵심 포인트

범주형 결과를 예측해야 하는 경우 로지스틱 회귀에서, 단순한 분류뿐만 아니라 각 클래스에 속할 확률까지 함께 제공할 수 있다.

## 📝 설명

### 로지스틱 회귀가 확률 기반 분류에 적합한 이유

로지스틱 회귀는 선형 회귀와 달리 시그모이드(sigmoid) 함수를 사용하여 입력값을 0과 1 사이의 확률값으로 변환합니다. 이는 단순히 "A 또는 B"로 분류하는 것을 넘어서 "A일 확률이 85%"와 같은 구체적인 확신도를 제공합니다.

핵심 수식: `p = 1 / (1 + e^(-z))`
여기서 z = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ

### 아키텍처 플로우

```
입력 변수들 (x₁, x₂, ..., xₙ)
    ↓
선형 결합 (z = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ)
    ↓
시그모이드 함수 적용 (p = 1/(1+e^(-z)))
    ↓
확률값 출력 (0 ≤ p ≤ 1)
    ↓
임계값 기준 분류 (일반적으로 0.5)
```

### Trade-offs 고려사항

**로지스틱 회귀 장점**:
- 확률값을 직접 제공하여 불확실성 정량화 가능
- 해석이 용이하고 계산 속도가 빠름
- 오버피팅 위험이 상대적으로 낮음
- 기준선(baseline) 모델로 활용하기 적합

**로지스틱 회귀 단점**:
- 선형 관계를 가정하므로 복잡한 비선형 패턴 포착 어려움
- 특성 스케일링에 민감함
- 이상치(outlier)에 영향을 받을 수 있음

**복잡한 알고리즘 (Random Forest, Neural Network) 장점**:
- 비선형 관계와 복잡한 패턴 학습 가능
- 특성 간의 상호작용 자동 포착

**복잡한 알고리즘 단점**:
- 해석이 어려움 (블랙박스)
- 오버피팅 위험성 높음
- 계산 비용이 크고 훈련 시간이 오래 걸림

## 🔍 주요개념

### S자 곡선의 특징

- **시그모이드 함수**: 모든 실수값을 0과 1 사이로 변환하는 S자 모양의 함수
- **임계값 분류**: 일반적으로 0.5를 기준으로 이진 분류 수행
- **확률 해석**: 출력값을 직접 확률로 해석 가능

### 실전 적용

- **의료 진단**: 환자 정보 기반 질병 발병 확률 예측 (예: 심장병 위험도 85%)
- **스팸 분류**: 이메일 특성 기반 스팸 확률 계산 (예: 스팸일 확률 73%)
- **마케팅**: 고객 행동 예측과 구매 확률 추정 (예: 이탈 확률 42%)
- **금융**: 신용 평가와 대출 승인 확률 산정 (예: 연체 위험 18%)

## 📝 관련 문제

**Question:** A healthcare company wants to predict the probability of heart disease in patients based on age, blood pressure, and cholesterol levels. They need both the classification result and the confidence level for medical decision-making. Which approach is most suitable?

**Options:**

- A) Linear Regression with threshold-based classification
- B) Logistic Regression with sigmoid function
- C) K-means clustering for patient segmentation  
- D) Decision trees with binary splits
- E) Support Vector Machine with linear kernel

**정답: B) Logistic Regression with sigmoid function**

💡 추가 설명:

- **A) Linear Regression** - 연속값을 예측하므로 확률 범위(0-1)를 보장하지 못함
- **C) K-means clustering** - 비지도 학습으로 확률 예측이 아닌 그룹핑에 적합
- **D) Decision trees** - 확률보다는 규칙 기반 분류에 특화됨
- **E) Support Vector Machine** - 분류 경계는 명확하지만 직관적인 확률 해석이 어려움

---