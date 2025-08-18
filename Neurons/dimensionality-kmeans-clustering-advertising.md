---

title: dimensionality-kmeans-clustering-advertising
created: 2025-08-18
modified: 2025-08-18
tags:
- technique/clustering
- algorithm/kmeans
- problem/dimensionality-reduction
- industry/advertising
- usecase/purchase-prediction
aliases: ["dimension-reduction", "k-means-clustering", "demographic-segmentation"]

---

# 고차원 인구통계 데이터에서 K-Means 클러스터링을 활용한 차원 축소

## 🎯 핵심 포인트

수백 개의 인구통계 컬럼을 가진 고차원 데이터셋에서 구매 예측 모델을 효율적으로 훈련하려는 경우, K-Means 클러스터링으로 인구통계적 특성 기반의 그룹을 생성하여 차원을 축소할 수 있다.

## 📝 설명

### K-Means 클러스터링이 차원 축소에 적합한 이유

K-Means 클러스터링은 유사한 인구통계적 특성을 가진 개인들을 그룹화하여 효과적인 차원 축소를 제공합니다. 수백 개의 개별 인구통계 특성 대신 클러스터 레이블을 사용함으로써, 모델 훈련 효율성을 크게 향상시키면서도 의미 있는 고객 세그먼트 정보를 보존할 수 있습니다.

클러스터링 과정에서 각 개인은 가장 유사한 특성을 가진 클러스터에 할당되며, 이 클러스터 ID가 새로운 범주형 특성이 됩니다. 예를 들어, '고소득 도시 거주 청년층', '중소득 교외 중년층' 등의 의미 있는 세그먼트가 자동으로 생성됩니다.

### 아키텍처 플로우

```
원본 데이터 (수백 컬럼) 
    ↓
K-Means 클러스터링 알고리즘 적용
    ↓
개인별 클러스터 레이블 할당 (Cluster 1, 2, 3, ...)
    ↓
클러스터 레이블을 새로운 특성으로 사용
    ↓
차원이 축소된 데이터로 예측 모델 훈련
    ↓
구매 가능성 예측 및 타겟 마케팅 적용
```

### Trade-offs 고려사항

**K-Means 클러스터링 장점**:
- 명확한 차원 축소로 훈련 효율성 향상
- 해석 가능한 고객 세그먼트 생성
- 타겟 마케팅을 위한 실용적 그룹화
- 모델 복잡도 감소로 오버피팅 위험 완화

**K-Means 클러스터링 단점**:
- 클러스터 수(K) 사전 결정 필요
- 구형(spherical) 클러스터 가정의 한계
- 이상치에 민감함
- 일부 세부 정보 손실 가능성

**KNN (K-Nearest Neighbors) 장점**:
- 모든 특성 정보 보존
- 지역적 패턴 학습에 효과적

**KNN 단점**:
- 차원 축소 효과 없음 (모든 특성 사용)
- 고차원에서 성능 저하 (차원의 저주)
- 예측 알고리즘이지 차원 축소 기법이 아님

**Factorization Machine 장점**:
- 특성 간 상호작용 모델링에 우수
- 희소 데이터에서 효과적

**Factorization Machine 단점**:
- 주목적이 상호작용 탐지이지 차원 축소가 아님
- 복잡한 하이퍼파라미터 튜닝 필요

## 🔍 주요개념

### 핵심 개념 비교

- **K-Means 클러스터링**: 중심점(centroid) 기반으로 데이터를 k개 그룹으로 분할하는 비지도 학습 알고리즘
- **KNN (K-Nearest Neighbors)**: 새로운 데이터 포인트의 k개 근접 이웃을 찾아 분류/회귀하는 지연 학습 알고리즘
- **차원 축소**: 고차원 데이터를 저차원으로 변환하면서 중요한 정보는 보존하는 기법

### 실전 적용

- **고객 세그먼트 기반 개인화 마케팅**: 클러스터별 맞춤형 광고 캠페인 설계
- **리소스 효율적인 모델 훈련**: 제한된 컴퓨팅 환경에서 빠른 모델 개발
- **해석 가능한 예측 모델**: 비즈니스 이해관계자들이 쉽게 이해할 수 있는 세그먼트 기반 인사이트

## 📝 관련 문제

**Question:** An advertising company aims to develop a predictive model for the likelihood of purchase using a training dataset that contains hundreds of columns of demographic data, such as age, location, and income. The large dimensionality of this dataset poses a challenge for efficiently training the model. Given the absence of features representing broader groups of people, which of the following would be the most reasonable approach to reduce the dimensionality of the training data?

**Options:**

- A) Optimize Model Performance with Adaptive Learning Rates
- B) Use K-Means clustering to categorize individuals into groups based on demographic features, and adapt the model training to these clusters
- C) Employ K-Nearest Neighbors (KNN) to cluster individuals into demographic groups, using these clusters as a basis for targeted model training
- D) Apply a factorization machine algorithm to explore and model interactions within the training data for improved prediction accuracy

**정답: B) Use K-Means clustering to categorize individuals into groups based on demographic features, and adapt the model training to these clusters**

💡 추가 설명:

- **옵션 A** - 적응적 학습률은 모델 최적화를 개선하지만 차원 축소 문제를 해결하지 못함
- **옵션 C** - KNN은 클러스터링 알고리즘이 아니며, 모든 특성을 사용하여 차원 축소 효과가 없음
- **옵션 D** - Factorization Machine은 특성 상호작용 모델링에 특화되어 있으나, 차원 축소가 주목적이 아님

---