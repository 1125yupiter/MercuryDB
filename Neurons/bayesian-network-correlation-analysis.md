---

title: bayesian-network-correlation-analysis
created: 2025-08-16
modified: 2025-08-16
tags:
- algorithm/bayesian-network
- algorithm/naive-bayes
- method/classification
- technique/correlation-analysis
- concept/feature-dependency
aliases: ["bayesian-model-selection", "correlation-based-modeling", "feature-independence"]

---

# Bayesian Network vs Naive Bayes: 상관계수 기반 모델 선택

## 🎯 핵심 포인트

피어슨 상관계수가 0 근처(-0.1에서 0.1)가 아닌 넓은 범위(0.1에서 0.97)에 분포한 경우, 특성 간 통계적 종속성이 존재하므로 Full Bayesian Network를 선택할 수 있다.

## 📝 설명

### Bayesian Network가 높은 상관계수 데이터에 적합한 이유

피어슨 상관계수는 두 변수 간의 선형 관계를 측정하는 지표로, -1에서 1 사이의 값을 가집니다. 0에 가까울수록 독립성을 의미하고, 절댓값이 클수록 강한 종속성을 나타냅니다.

**상관계수와 모델 선택 기준**:
- **0 근처(-0.1~0.1)**: 특성 간 독립성 가정 가능 → Naive Bayes 적합
- **넓은 범위 분포(0.1~0.97)**: 특성 간 종속성 존재 → Full Bayesian Network 적합

### 아키텍처 플로우

```
데이터 입력
    ↓
피어슨 상관계수 계산
    ↓
상관계수 분포 분석
    ↓
┌─────────────────┐    ┌─────────────────┐
│ 0 근처(-0.1~0.1) │    │ 넓은 범위 분포    │
│ 독립성 가정 가능  │    │ 종속성 존재      │
└─────────────────┘    └─────────────────┘
    ↓                      ↓
Naive Bayes           Full Bayesian Network
(조건부 독립 가정)      (종속성 모델링)
```

### Trade-offs 고려사항

**Full Bayesian Network 장점**:
- 특성 간 복잡한 종속성 관계를 정확히 모델링
- 높은 상관계수를 가진 특성들의 관계를 활용하여 예측 성능 향상
- 실제 데이터의 구조를 더 정확하게 반영

**Full Bayesian Network 단점**:
- 계산 복잡도가 높아 학습 시간 증가
- 더 많은 데이터와 파라미터 필요
- 네트워크 구조 설계의 복잡성

**Naive Bayes 장점**:
- 빠른 학습과 추론 속도
- 적은 데이터로도 학습 가능
- 구현과 해석이 간단

**Naive Bayes 단점**:
- 강한 독립성 가정으로 인한 현실성 부족
- 높은 상관계수(0.97 등) 존재 시 성능 저하
- 특성 간 관계 정보 손실

## 🔍 주요개념

### 통계적 독립성 vs 종속성

- **통계적 독립성**: 한 변수의 값이 다른 변수의 값에 영향을 주지 않는 상태 (상관계수 ≈ 0)
- **통계적 종속성**: 변수들 간에 상관관계가 존재하는 상태 (상관계수 절댓값이 큰 경우)

### 실전 적용

- **의료 진단**: 증상들 간 높은 상관관계 존재 시 Full Bayesian Network로 정확한 진단 지원
- **금융 리스크**: 경제 지표들 간 강한 상관성을 고려한 리스크 모델링
- **마케팅 분석**: 고객 행동 특성 간 복잡한 관계를 반영한 타겟팅 시스템

## 📝 관련 문제

**Question:** A Machine Learning Specialist is wondering if he should build a full Bayesian Network or a Naive Bayesian model for a classification task. The computed Pearson correlation coefficients between each predictor range from 0.1 - 0.97. Based on this data, which model should the Specialist choose and why? (Select TWO.)

**Options:**

- A) Naive Bayesian model
- B) Full Bayesian network
- C) Some of the features are statistically dependent
- D) All of the features are statistically independent
- E) Some of the features are conditionally independent

**정답: B) Full Bayesian network, C) Some of the features are statistically dependent**

💡 추가 설명:

- **A) Naive Bayesian model** - 상관계수가 0.97까지 있어 강한 종속성이 존재하므로 독립성 가정이 위배됨
- **D) All of the features are statistically independent** - 0.97의 높은 상관계수는 강한 양의 상관관계를 의미하므로 독립적이지 않음
- **E) Some of the features are conditionally independent** - 피어슨 상관계수는 두 변수 간의 절대적 관계만 측정하므로 조건부 독립성은 판단할 수 없음