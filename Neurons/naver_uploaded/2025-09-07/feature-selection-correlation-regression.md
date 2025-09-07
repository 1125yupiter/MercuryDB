---

title: feature-selection-correlation-regression
created: 2025-08-24
modified: 2025-08-24
tags:
- method/feature-selection
- algorithm/linear-regression
- technique/correlation
- problem/regression
- evaluation/model-simplification
aliases: ["feature-selection", "correlation-analysis", "model-simplification"]

---

# 다변수 선형 회귀에서 특성 선택을 통한 모델 단순화

## 🎯 핵심 포인트

주택 가격 예측과 같은 다변수 선형 회귀 모델을 구축하는 경우 목표 변수와의 상관관계 분석에서, 상관관계가 낮은 특성을 제거하여 모델을 단순화할 수 있다.

## 📝 설명

### 특성 선택(Feature Selection)이 모델 성능 향상에 적합한 이유

특성 선택은 머신러닝 모델의 성능을 향상시키고 복잡도를 줄이는 핵심 전처리 단계입니다. 특히 다변수 선형 회귀에서는 목표 변수와 직접적인 관련성이 높은 특성들만 선택하는 것이 중요합니다. 불필요한 특성들은 노이즈를 증가시키고 과적합(overfitting)을 유발할 수 있으며, 모델의 해석 가능성을 떨어뜨립니다.

### 특성 선택 방법론 플로우

```
원시 데이터셋
    ↓
탐색적 데이터 분석 (EDA)
    ↓
특성별 분산 분석
    ↓
목표 변수와 상관관계 분석 ← [핵심 단계]
    ↓
상관관계 임계값 설정 (예: |r| < 0.1)
    ↓
저상관 특성 제거
    ↓
최종 특성 세트로 모델 훈련
```

### Trade-offs 고려사항

**목표 변수 상관관계 기반 특성 선택 장점**:

- 예측 성능에 직접적으로 기여하지 않는 특성 제거
- 모델 복잡도 감소로 인한 해석 가능성 향상
- 과적합 위험 감소
- 훈련 시간 단축

**목표 변수 상관관계 기반 특성 선택 단점**:

- 선형 관계만 포착 가능 (비선형 관계 놓칠 수 있음)
- 특성 간 상호작용 효과 무시
- 임계값 설정의 주관성

**분산 기반 특성 선택 장점**:

- 정보량이 적은 상수형 특성 제거 가능
- 계산이 간단하고 빠름

**분산 기반 특성 선택 단점**:

- 목표 변수와의 관계를 고려하지 않음
- 낮은 분산이지만 중요한 특성을 제거할 위험

## 🔍 주요개념

### 특성 선택 방법론 비교

- **필터 방법(Filter Methods)**: 통계적 측정치 기반 (상관관계, 분산, 카이제곱 등)
- **래퍼 방법(Wrapper Methods)**: 모델 성능 기반 선택 (순차 선택, 유전 알고리즘)
- **임베디드 방법(Embedded Methods)**: 모델 훈련 과정에서 자동 선택 (L1 정규화, 트리 기반 중요도)

### 실전 적용

- 부동산 가격 예측에서 우편번호별 상관관계 분석을 통한 지역 변수 선택
- 고객 이탈 예측에서 행동 패턴 변수들의 목표 변수 상관도 기반 필터링
- 매출 예측 모델에서 계절성 특성과 매출 간 상관관계를 활용한 변수 축약

## 📝 관련 문제

**Question:** A corporation wishes to forecast home selling prices using existing historical sales data. The selling price is the goal variable in the company's dataset. The attributes include the lot size, measures of the living space and non-living area, the number of bedrooms and bathrooms, the year constructed, and the postal code. The organization wishes to forecast home selling prices using multivariable linear regression. Which step should a machine learning professional take to eliminate extraneous information and simplify the model?

**Options:**

- A) Plot a histogram of the features and compute their standard deviation. Remove features with high variance.
- B) Plot a histogram of the features and compute their standard deviation. Remove features with low variance.
- C) Build a heatmap showing the correlation of the dataset against itself. Remove features with low mutual correlation scores.
- D) Run a correlation check of all features against the target variable. Remove features with low target variable correlation scores.

**정답: D) Run a correlation check of all features against the target variable. Remove features with low target variable correlation scores.**

💡 추가 설명:

- **Option A** - 높은 분산 특성은 일반적으로 더 많은 정보를 담고 있어 제거하면 안됨
- **Option B** - 낮은 분산 특성 제거는 유용하지만 목표 변수와의 관계를 고려하지 않아 불완전함
- **Option C** - 특성 간 상관관계가 낮다고 해서 목표 변수 예측에 불필요한 것은 아님
- **정답 D** - 목표 변수와 상관관계가 낮은 특성이야말로 예측에 기여도가 낮아 제거 대상임

---