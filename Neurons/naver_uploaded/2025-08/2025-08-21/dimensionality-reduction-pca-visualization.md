---
title: dimensionality-reduction-pca-visualization
created: 2024-05-18
modified: 2024-05-18
tags:

  - technique/dimensionality-reduction
  - method/unsupervised
  - algorithm/pca
  - problem/linear-regression
  - constraint/high-dimensional
aliases: ["principal component analysis", "PCA"]
---

# 고차원 데이터 시각화를 위한 주성분 분석(PCA) 활용

## 🎯 핵심 포인트

수천 개의 피처를 가진 고차원 데이터셋에서 선형 회귀 모델을 훈련할 때, 초기 데이터 시각화 및 이해를 위해 \*\*주성분 분석(PCA)\*\*을 사용하여 상관관계가 높은 피처의 수를 줄이고 데이터를 시각화할 수 있다.

-----

## 📝 설명

### 주성분 분석(PCA)이 고차원 데이터에 적합한 이유

\*\*주성분 분석(PCA)\*\*은 \*\*차원 축소(Dimensionality Reduction)\*\*를 위한 비지도 학습 알고리즘입니다. 데이터에 존재하는 수많은 피처 중 서로 상관관계가 높은 피처들을 새로운 소수의 피처 집합인 \*\*주성분(Principal Components)\*\*으로 변환합니다. 이 주성분들은 원래 데이터의 분산을 최대한 설명하도록 설계되어, 데이터의 가장 중요한 정보를 압축하여 담아냅니다. 이를 통해 고차원 데이터의 복잡성을 줄이고, 2\~3개의 주성분만으로도 데이터의 패턴을 시각화하여 이해하는 것이 가능해집니다.

### 아키텍처 플로우

```
원래 데이터셋 (수천 개의 피처)
       ↓
    데이터 표준화 (평균 0, 분산 1)
       ↓
    공분산 행렬 계산 (모든 피처 쌍의 관계 파악)
       ↓
    고유 벡터와 고유 값 계산 (데이터 분산의 주요 방향과 중요도 파악)
       ↓
    주성분 선택 (중요한 고유 값을 가진 상위 K개 고유 벡터 선택)
       ↓
    데이터 변환 (새로운 주성분 공간으로 데이터 투영)
       ↓
축소된 데이터셋 (K개의 주성분)
       ↓
    시각화 및 모델 훈련
```

### Trade-offs 고려사항

**주성분 분석(PCA) 장점**:

  - **차원 축소**: 수많은 피처를 소수의 주성분으로 압축하여 모델 학습 속도를 향상시키고 과적합을 방지합니다.
  - **데이터 시각화**: 고차원 데이터를 2D 또는 3D로 시각화하여 데이터의 패턴을 쉽게 파악할 수 있습니다.
  - **다중공선성 제거**: 서로 상관관계가 높은 피처들을 독립적인 주성분으로 변환하여 다중공선성 문제를 해결합니다.

**주성분 분석(PCA) 단점**:

  - **설명력 손실**: 일부 정보가 손실될 수 있으며, 주성분이 원래 피처와 직접적으로 연결되지 않아 해석이 어려울 수 있습니다.
  - **선형성 가정**: 데이터의 관계가 선형적일 때 가장 효과적입니다. 비선형 관계는 잘 포착하지 못할 수 있습니다.

**피어슨 상관 계수 장점**:

  - 두 변수 간의 선형적 관계 강도를 직관적으로 파악할 수 있습니다.

**피어슨 상관 계수 단점**:

  - 단순히 관계를 측정할 뿐, 피처의 수를 직접적으로 줄이는 차원 축소 기능은 없습니다.

-----

## 🔍 주요개념

### 핵심 개념 비교

  - **공분산(Covariance)**: 두 변수가 함께 어떻게 변화하는지 나타내는 지표. 양의 공분산은 함께 증가하는 경향을, 음의 공분산은 반대로 움직이는 경향을 의미합니다.
  - **주성분(Principal Component)**: PCA를 통해 생성된 새로운 피처. 서로 독립적이며, 원래 데이터의 분산을 최대한 설명하는 방향을 나타냅니다.

### 실전 적용

  - **의료 이미지 분석**: MRI나 CT 스캔 이미지의 수많은 픽셀 데이터를 소수의 주성분으로 축소하여 질병을 더 쉽게 감지합니다.
  - **주식 시장 예측**: 여러 주식의 가격 변동 데이터를 소수의 주성분으로 압축하여 시장의 주요 동향을 파악하고 예측 모델의 성능을 개선합니다.
  - **고객 세분화**: 수십 개의 고객 구매 행동 데이터를 몇 개의 주성분으로 줄여 고객을 그룹화하고, 각 그룹의 특성을 더 쉽게 이해할 수 있습니다.

## 📝 관련 문제

**Question:** A Machine Learning Specialist works on a dataset with several thousands of features to train a linear regression model. Initial data visualization shows many highly correlated values. The Specialist wants to reduce this number so she can visualize and understand the data easier. How can the Specialist achieve this?

**Options:**

  - A) Use the Pearson correlation coefficient to determine and reduce highly correlated features.
  - B) Reduce the highly correlated features using matrix multiplication.
  - C) Reduce the highly correlated features using one-hot encoding.
  - D) Apply the Principal Component Analysis (PCA) algorithm.
  - E) There is no correct answer.

**정답: D) Apply the Principal Component Analysis (PCA) algorithm.**

💡 추가 설명:

  - **오답 옵션 A)** - 피어슨 상관 계수는 관계를 측정하는 지표일 뿐, 피처의 수를 줄이는 알고리즘이 아닙니다.
  - **오답 옵션 B)** - 행렬 곱셈은 데이터셋의 크기를 줄이는 데 직접적인 도움이 되지 않으며, 상관관계가 있는 피처를 줄이는 목적에 부합하지 않습니다.
  - **오답 옵션 C)** - 원-핫 인코딩은 범주형 피처를 바이너리 벡터로 변환하는 것으로, 오히려 피처의 수를 증가시킬 수 있습니다.
  - **오답 옵션 E)** - PCA가 명확한 해결책이므로, 이 옵션은 옳지 않습니다.