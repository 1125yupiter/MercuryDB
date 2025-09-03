---
title: classification-svm-imbalanced-lowlatency
created: 2025-08-17
modified: 2025-08-17
tags:
  - problem/classification
  - method/supervised
  - algorithm/svm
  - constraint/imbalanced-data
  - constraint/low-latency
aliases: ["Support Vector Machine", "SVM", "RBF Kernel", "non-linear classification"]
---

# 비선형 데이터 분류에 적합한 Support Vector Machine (SVM)

## 🎯 핵심 포인트

데이터 분포가 선형적으로 분리되지 않는 경우, SVM with RBF Kernel을 사용하여 비선형 결정 경계를 생성하고 높은 정확도로 분류할 수 있다.

## 📝 설명

### SVM with RBF Kernel가 사기 탐지에 적합한 이유

SVM은 주로 분류 작업에 사용되는 **선형 모델**입니다. 이 알고리즘은 데이터를 분리하는 \*\*최적의 선형 초평면(hyperplane)\*\*을 찾는 것이 목표입니다. 하지만, 실제 데이터는 종종 선형적으로 분리되지 않는 경우가 많습니다.

주어진 산점도와 같이 비선형적으로 분포된 데이터를 효과적으로 분류하기 위해 SVM은 \*\*커널 트릭(Kernel Trick)\*\*이라는 기술을 활용합니다. 이 기술은 데이터를 물리적으로 고차원 공간으로 옮기지 않고도, 마치 고차원으로 옮긴 것처럼 계산을 수행하여 **선형 분리가 가능한 상태**로 만들어줍니다.

특히, **Radial Basis Function (RBF) Kernel**은 이러한 비선형 문제를 해결하는 데 매우 효과적입니다. RBF 커널을 적용한 SVM은 데이터를 고차원 공간으로 변환하여, 그곳에서 선형 초평면을 찾습니다. 이 초평면을 다시 2차원 공간으로 투영하면 **비선형 결정 경계**가 생성되어 복잡한 데이터 분포를 정확하게 분류할 수 있게 됩니다.

따라서, SVM 자체는 선형 모델이지만, **RBF 커널을 통해 비선형 문제를 해결하는 데 최적의 성능**을 발휘하며, 이는 복잡한 사기 거래 패턴을 분류하는 데 가장 적합한 이유입니다.

### 아키텍처 플로우

```
2D 비선형 데이터 (훈련 데이터)
↓
SVM 모델 (RBF 커널 사용)
↓
커널 트릭을 통한 고차원 공간 매핑 및 최적의 초평면 찾기
↓
새로운 2D 데이터 (검증 데이터)
↓
초평면 기반의 비선형 결정 경계를 통한 분류 예측 (정상 vs. 사기)
```

### Trade-offs 고려사항

**SVM with RBF Kernel 장점**:

  - **비선형 데이터 분류**: 커널 트릭을 통해 복잡한 데이터 분포를 가진 문제에 매우 효과적입니다.
  - **높은 정확도**: 적절한 파라미터(gamma, C) 튜닝 시 높은 예측 정확도를 달성할 수 있습니다.
  - **과적합 방지**: 초평면 주변의 지지 벡터(Support Vectors)만 사용하므로 과적합 위험이 상대적으로 낮습니다.

**SVM with RBF Kernel 단점**:

  - **계산 비용**: 훈련 데이터셋이 클수록 계산 시간이 오래 걸립니다.
  - **파라미터 튜닝**: 최적의 성능을 위해 `gamma`와 `C` 같은 하이퍼파라미터 튜닝이 필수적입니다.
  - **해석의 어려움**: 복잡한 커널을 사용하기 때문에 모델의 결정 과정을 해석하기 어렵습니다.

**Logistic Regression 장점**:

  - **선형 분류**: 선형적으로 분리 가능한 데이터에 대해 간단하고 빠르며 효과적입니다.
  - **모델 해석 용이**: 각 피처가 결과에 미치는 영향을 쉽게 해석할 수 있습니다.

**Logistic Regression 단점**:

  - **비선형 데이터 부적합**: 주어진 문제와 같이 비선형적으로 분포된 데이터에는 정확도가 매우 낮습니다.

## 🔍 주요개념

### 커널 트릭 (Kernel Trick)

  - **정의**: 데이터를 물리적으로 고차원 공간으로 옮기지 않고도, 마치 고차원으로 옮긴 것처럼 내적(dot product) 계산을 수행하여 선형 분리가 가능하게 하는 기술.
  - **RBF 커널**: $K(x, x') = \\exp(-\\gamma ||x - x'||^2)$ 와 같은 형태로, 비선형 데이터를 효과적으로 변환하는 데 사용됩니다.

### 지지 벡터 (Support Vectors)

  - **정의**: 결정 경계에 가장 가까이 위치한 데이터 포인트들. SVM은 이 지지 벡터들을 기반으로 최적의 초평면을 찾습니다.

### 실전 적용

  - **신용카드 사기 탐지**: 정상 거래와 사기 거래의 복잡한 패턴을 분류.
  - **의료 진단**: 양성/악성 종양 여부 판별과 같이 복잡한 의료 데이터를 분류.
  - **이미지 분류**: 이미지 픽셀 간의 복잡한 관계를 학습하여 객체 인식.

## 📝 관련 문제

**Question:** A Machine Learning Specialist working for a security company is building a supervised fraud detection model. The Specialist used a scatter plot to inspect the relationship between the two features the model will be based on. The plot is illustrated below (authentic transactions are denoted by 0, and the fraudulent ones are denoted by 1). Which algorithm would MOST likely produce the highest accuracy given the class distribution above?

**Options:**

  - A) K-Means algorithm with an epoch number of 5
  - B) Logistic Regression
  - C) Random Cut Forest (RCF) algorithm
  - D) Support Vector Machines (SVM) with Radial Basis Function (RBF) Kernel
  - E) Gaussian Naive Bayes

**정답: D) Support Vector Machines (SVM) with Radial Basis Function (RBF) Kernel**

💡 추가 설명:

  - **A) K-Means algorithm**: 비지도 학습 알고리즘으로 레이블이 있는 데이터 분류에는 적합하지 않습니다.
  - **B) Logistic Regression**: 선형 분류기이므로 비선형적으로 분포된 데이터를 분류하는 데 부적합합니다.
  - **C) Random Cut Forest (RCF) algorithm**: 이상 탐지에 사용되는 비지도 학습 알고리즘으로, 레이블 기반의 분류 문제에는 적합하지 않습니다.
  - **E) Gaussian Naive Bayes**: 간단한 확률 기반 분류기이며, 데이터 분포가 복잡하고 비선형적인 경우에는 성능이 떨어질 수 있습니다.