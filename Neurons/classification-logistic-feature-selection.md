---

title: classification-logistic-feature-selection
created: 2025-08-26
modified: 2025-08-26
tags:
- algorithm/logistic-regression
- technique/feature-selection
- method/rfe
- constraint/interpretable
- problem/classification
aliases: ["logistic-regression", "rfe", "feature-selection", "classification"]

---

# 분류 문제에서 로지스틱 회귀와 특성 선택 기법

## 🎯 핵심 포인트

과적합이 발생한 로지스틱 회귀 모델에서 해석 가능성과 중요 특성 식별이 필요한 경우, L1 정규화와 RFE(Recursive Feature Elimination)를 통해 모델 성능을 최적화하고 비즈니스 요구사항을 충족할 수 있다.

## 📝 설명

### 로지스틱 회귀가 해석 가능한 분류에 적합한 이유

로지스틱 회귀는 시그모이드 함수를 사용하여 선형 결합을 0~1 사이의 확률값으로 변환하는 분류 모델이다. 각 특성의 계수(coefficient)를 통해 해당 특성이 결과에 미치는 직접적인 영향을 정량적으로 해석할 수 있어, 마케팅이나 의료 등 의사결정 근거가 중요한 분야에서 선호된다.

**로지스틱 회귀의 핵심 구조:**
```
P(y=1) = 1 / (1 + e^(-(β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ)))
```

여기서 각 계수 βᵢ는 해당 특성이 1단위 증가할 때 로그 오즈(log odds)의 변화량을 나타내며, 이를 통해 "특성 A가 1 증가하면 이탈 확률이 X% 증가한다"는 식의 비즈니스 해석이 가능하다.

### 아키텍처 플로우

```
원본 데이터 (100개 특성)
        ↓
L1 정규화 적용 (일부 계수를 0으로 만들어 자동 특성 선택)
        ↓
RFE 수행 (체계적 특성 제거)
Step 1: 전체 특성으로 모델 훈련
Step 2: 중요도 평가 (계수 크기 기준)
Step 3: 가장 중요하지 않은 특성 제거
Step 4: 재훈련 및 평가
Step 5: 목표 특성 개수까지 반복
        ↓
최종 모델 (중요한 20개 특성)
        ↓
계수 해석 → 비즈니스 액션
```

### Trade-offs 고려사항

**L1 정규화 장점**:
- 자동 특성 선택 효과 (sparse solution)
- 과적합 방지
- 모델 해석 가능성 향상
- 계산 효율성 증대

**L1 정규화 단점**:
- 상관관계가 높은 특성 중 하나만 선택하는 경향
- 정규화 강도(λ) 하이퍼파라미터 튜닝 필요
- 그룹화된 특성들의 관계를 놓칠 수 있음

**RFE 장점**:
- 모델 성능을 직접 평가하여 특성 선택
- 체계적이고 안정적인 접근
- 특성 간 상호작용 고려
- 실제 예측 기여도 기반 평가

**RFE 단점**:
- 계산 비용이 높음 (여러 번 재훈련)
- 시간이 오래 걸림
- 목표 특성 개수를 미리 설정해야 함

**t-SNE 부적절성**:
- 비선형 변환으로 원본 특성의 해석 불가
- 모델 출력과 직접 연결되지 않음
- "직접적 효과" 측정 불가능
- 시각화용 도구로 예측 모델링에 부적합

## 🔍 주요개념

### 정규화 기법 비교

- **L1 정규화 (Lasso)**: 계수의 절댓값의 합을 제한하여 일부 계수를 정확히 0으로 만들어 자동 특성 선택
- **L2 정규화 (Ridge)**: 계수의 제곱의 합을 제한하여 계수 크기를 줄이지만 0으로 만들지는 않음

### 실전 적용

- **고객 이탈 예측**: "서비스 통화 횟수가 1회 증가하면 이탈 확률 30% 증가" → 고객 서비스 품질 개선 전략
- **의료 진단**: "혈압이 10mmHg 증가하면 질병 확률 15% 증가" → 예방 의학적 접근
- **마케팅 캠페인**: "이전 구매 횟수가 1회 증가하면 재구매 확률 25% 증가" → 고객 세분화 전략

## 📝 관련 문제

**Question:** A Data Scientist is developing a model to predict customer attrition using a dataset of 100 continuous numerical characteristics. The Marketing department wants to interpret the model and determine the direct effect of important characteristics on the model's output. During logistic regression training, there's a significant difference between training and validation accuracy. Which techniques can optimize performance and meet Marketing team requirements?

**Options:**

- A) Add L1 regularization to the classifier
- B) Add features to the dataset
- C) Perform recursive feature elimination
- D) Perform t-distributed stochastic neighbor embedding (t-SNE)
- E) Perform linear discriminant analysis

**정답: A) L1 regularization + C) Recursive Feature Elimination**

💡 추가 설명:

- **B) Add features** - 이미 과적합 상황에서 특성을 추가하면 문제가 더 악화됨
- **D) t-SNE** - 비선형 차원 축소로 원본 특성의 해석이 불가능하고 직접적 효과 측정 불가
- **E) Linear Discriminant Analysis** - 차원 축소는 가능하지만 로지스틱 회귀만큼 해석이 용이하지 않음