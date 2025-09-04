---

title: customer-segmentation-clustering-unsupervised
created: 2025-08-23
modified: 2025-08-23
tags:
- problem/segmentation
- method/unsupervised
- algorithm/kmeans
- constraint/unclear-labels
- usecase/customer-analysis
aliases: ["customer-segmentation", "clustering-vs-classification", "unsupervised-segmentation"]

---

# 고객 세분화 - K-means 클러스터링 vs 지도학습 분류

## 🎯 핵심 포인트

고객 세분화 기준이 불분명한 경우, 지도학습 대신 k-means와 같은 비지도학습 클러스터링을 사용하여 데이터 패턴을 먼저 발견할 수 있다.

## 📝 설명

### K-means Clustering이 불분명한 고객 세분화에 적합한 이유

현재 고객 세분화가 명확하지 않은 상황에서는 어떤 기준으로 고객을 나눠야 할지 모르는 상태입니다. 이런 경우 지도학습 알고리즘(XGBoost, k-NN 등)은 사전에 정의된 라벨이 필요하므로 적용하기 어렵습니다. 

K-means 클러스터링은 비지도학습 방법으로, 나이, 소득, 지역 등의 고객 데이터만으로 유사한 특성을 가진 고객들을 자동으로 그룹화합니다. 이를 통해 자연스러운 고객 세분화 패턴을 발견하고, 이후 맞춤형 마케팅 전략을 수립할 수 있습니다.

### 아키텍처 플로우

```
고객 데이터 (나이, 소득, 지역)
        ↓
    K-means 알고리즘 (K=5)
        ↓
자동 클러스터링 (5개 그룹)
        ↓
각 그룹 특성 분석
        ↓
맞춤형 마케팅 전략 수립
```

### Trade-offs 고려사항

**K-means Clustering 장점**:
- 라벨이 없어도 패턴 발견 가능
- 데이터 기반의 객관적 그룹화
- 새로운 세분화 관점 제공
- 구현이 상대적으로 간단

**K-means Clustering 단점**:
- 최적의 K값 결정이 주관적
- 구형 클러스터 가정의 한계
- 결과 해석에 도메인 지식 필요

**XGBoost (지도학습) 장점**:
- 높은 예측 정확도
- 복잡한 패턴 학습 가능
- 특성 중요도 제공

**XGBoost (지도학습) 단점**:
- 명확한 라벨 필요 (현재 상황에 부적합)
- 기존 편향된 분류 기준 재생산 가능성
- 새로운 세분화 패턴 발견 어려움

## 🔍 주요개념

### 학습 방법 비교

- **지도학습 (Supervised Learning)**: 입력-출력 쌍이 있는 데이터로 학습, 명확한 정답 라벨 필요
- **비지도학습 (Unsupervised Learning)**: 라벨 없는 데이터에서 숨겨진 패턴이나 구조 발견

### 실전 적용

- **이커머스 고객 세분화**: 구매 패턴, 방문 빈도, 구매 금액으로 자동 그룹화
- **신규 시장 진입**: 기존 분류 기준 없는 새로운 고객층 파악
- **제품 라인 확장**: 다양한 고객 니즈 그룹 발견 및 제품 개발 방향성 수립

## 📝 관련 문제

**Question:** A weekly email newsletter is sent to all of an ecommerce company's clients. Management has enlisted the assistance of a team of writers to provide further tailored material. A data scientist must categorize customers depending on their age, income, and geography. The current segmentation of consumers is unclear. Previously, the data scientist developed an XGBoost model to forecast a customer's propensity to reply to an email based on their age, income, and location. Why does the XGBoost model fail to fulfill current needs, and what can be done to rectify this?

**Options:**

- A) The XGBoost model provides a true/false binary output. Apply principal component analysis (PCA) with five feature dimensions to predict a segment.
- B) The XGBoost model provides a true/false binary output. Increase the number of classes the XGBoost model predicts to five classes to predict a segment.
- C) The XGBoost model is a supervised machine learning algorithm. Train a k-Nearest-Neighbors (kNN) model with K = 5 on the same dataset to predict a segment.
- D) The XGBoost model is a supervised machine learning algorithm. Train a k-means model with K = 5 on the same dataset to predict a segment.

**정답: D) K-means clustering**

💡 추가 설명:

- **선택지 A (PCA)** - 차원 축소 기법이지 고객 세분화 방법이 아님. 5개 특성으로 줄여도 그룹 분류는 불가
- **선택지 B (XGBoost 클래스 확장)** - 5개 클래스로 확장하려면 사전 정의된 라벨이 필요한데, 현재 "unclear segmentation" 상황에서는 불가능
- **선택지 C (k-NN)** - 여전히 지도학습으로 기존 라벨 기반 예측만 가능, 새로운 세분화 패턴 발견 불가
- **핵심 문제**: "어떻게 다섯 개로 나눌까"라는 분류 기준 자체가 불분명한 상황에서는 비지도학습으로 패턴을 먼저 발견해야 함