---

title: feature-selection-univariate-pricing
created: 2025-08-23
modified: 2025-08-23
tags:
- method/univariate-selection
- problem/feature-selection
- technique/statistical-testing
- constraint/high-dimensionality
- usecase/price-prediction
aliases: ["univariate", "feature-selection", "statistical-selection"]

---

# Feature Selection을 위한 Univariate Selection 방법론

## 🎯 핵심 포인트

1,000개 이상의 고차원 특성을 가진 가격 예측 문제에서 통계적 유의성을 바탕으로 각 특성을 개별적으로 평가하여 타겟과 가장 강한 관계를 가진 특성들을 선택할 수 있다.

## 📝 설명

### Univariate Selection이 고차원 특성 선택에 적합한 이유

Univariate Selection은 각 특성을 **개별적으로** 타겟 변수와의 관계를 평가하는 방법입니다. "Univariate"라는 말 그대로 한 번에 하나의 변수만 고려하여, F-statistic, Mutual Information, Chi-square 등의 통계적 검정을 통해 각 특성의 중요도를 정량화합니다. 특히 1,000개 이상의 특성을 가진 데이터에서 계산 효율성과 해석 용이성을 동시에 제공합니다.

### 처리 플로우

```
원본 데이터 (1000+ 특성)
        ↓
각 특성별 개별 통계 검정
(F-test, Chi-square, Mutual Information)
        ↓
통계적 유의성 점수 계산
        ↓
점수 기반 특성 순위화
        ↓
상위 N개 특성 선택
        ↓
선택된 특성으로 모델 학습
```

### Trade-offs 고려사항

**Univariate Selection 장점**:
- 계산 효율성이 높아 대용량 데이터 처리 가능
- 각 특성의 중요도 점수를 명확히 제공
- 통계적 근거에 기반한 객관적 선택
- 확장성이 뛰어나 특성 수가 많아도 처리 가능

**Univariate Selection 단점**:
- 특성 간 상호작용(interaction) 효과를 무시
- 중복성 문제 - 유사한 특성들이 모두 선택될 수 있음
- 일부 방법은 비선형 관계 포착에 한계

**Tree-based Feature Importance 장점**:
- 특성 간 상호작용을 자동으로 고려
- 비선형 관계까지 포착 가능

**Tree-based Feature Importance 단점**:
- 계산 비용이 높아 초기 특성 수가 많으면 비효율적
- 트리 구조에 의존적인 편향 가능성

## 🔍 주요개념

### 통계적 검정 방법 비교

- **F-statistic**: 연속형 타겟(가격)과 각 특성 간의 선형 관계 강도를 측정하는 ANOVA 기반 방법
- **Mutual Information**: 비선형 관계까지 포착할 수 있는 정보 이론 기반 방법
- **Chi-square**: 범주형 특성과 범주형 타겟 간의 독립성을 검정하는 방법
- **Correlation Analysis**: 선형 상관관계를 직접적으로 측정하는 방법

### 실전 적용

- **1단계 스크리닝**: 1,000개 특성을 Univariate Selection으로 200개로 축소
- **다단계 조합**: Tree-based importance와 결합하여 최종 50개 특성 선택
- **교차 검증**: 선택된 특성들의 일반화 성능을 검증하여 과적합 방지

## 📝 관련 문제

**Question:** A manufacturer of mobile devices wishes to establish the optimal selling price for its products. The firm has over 1,000 characteristics and wants to determine which ones are most important in determining the selling price. Which feature selection strategies should the business employ?

**Options:**

- A) Data scaling with standardization and normalization
- B) Correlation plot with heat maps
- C) Data binning  
- D) Univariate selection
- E) Feature importance with a tree-based classifier
- F) Data augmentation

**정답: B, D, E) Correlation analysis, Univariate selection, Tree-based feature importance**

💡 추가 설명:

- **Data scaling (A)** - 특성 선택이 아닌 데이터 전처리 방법
- **Data binning (C)** - 연속형 데이터를 범주형으로 변환하는 전처리 기법
- **Data augmentation (F)** - 데이터를 인위적으로 늘리는 방법으로 특성 선택과 무관

---