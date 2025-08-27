---

title: clustering-unsupervised-segmentation
created: 2025-08-27
modified: 2025-08-27
tags:
- problem/clustering
- method/unsupervised
- algorithm/kmeans
- usecase/segmentation
- constraint/unlabeled-data
aliases: ["customer-segmentation", "unsupervised-clustering", "kmeans"]

---

# 고객 세그멘테이션을 위한 비지도학습 클러스터링 적용

## 🎯 핵심 포인트

정답 라벨이 없는 고객 데이터에서 세그멘테이션이 필요한 경우, 지도학습 대신 K-means 같은 비지도학습 클러스터링 알고리즘을 사용할 수 있다.

## 📝 설명

### 비지도학습이 고객 세그멘테이션에 적합한 이유

고객 세그멘테이션은 기본적으로 **정답이 미리 정해지지 않은** 상황입니다. 어떤 고객이 어느 그룹에 속해야 하는지 사전에 알 수 없기 때문에, 데이터 내의 숨겨진 패턴을 찾아 자동으로 그룹을 만드는 비지도학습이 필수적입니다.

K-means 클러스터링은 고객의 나이, 소득, 지역 등의 특성을 바탕으로 유사한 특성을 가진 고객들을 자동으로 묶어주며, K값을 조정하여 원하는 세그먼트 수를 정확히 생성할 수 있습니다.

### 아키텍처 플로우

```
고객 데이터 (나이, 소득, 지역)
    ↓
데이터 전처리 및 정규화
    ↓
K-means 클러스터링 (K=5)
    ↓
5개 고객 세그먼트 생성
    ↓
각 세그먼트별 타겟팅 콘텐츠 제작
```

### Trade-offs 고려사항

**K-means 장점**:
- 정답 라벨 없이도 세그멘테이션 가능
- 계산이 빠르고 구현이 간단
- 원하는 클러스터 개수를 직접 지정 가능
- 대용량 데이터에도 효율적으로 작동

**K-means 단점**:
- 클러스터 개수 K를 사전에 결정해야 함
- 구형(spherical) 클러스터 가정으로 복잡한 모양 감지 한계
- 초기 중심점에 따라 결과가 달라질 수 있음

**XGBoost 장점**:
- 높은 예측 정확도
- 피처 중요도 제공

**XGBoost 단점**:
- 지도학습으로 정답 라벨이 반드시 필요
- 현재 상황처럼 세그먼트가 미지인 경우 사용 불가
- 분류 문제로 접근 시 이진/다중 분류 결과만 제공

## 🔍 주요개념

### 학습 방법 비교

- **지도학습(Supervised Learning)**: 입력과 정답이 모두 주어진 상태에서 학습. "이 고객은 세그먼트 A"라는 라벨이 필요
- **비지도학습(Unsupervised Learning)**: 입력 데이터만으로 숨겨진 패턴 발견. 정답 없이 유사한 데이터끼리 그룹화

### 실전 적용

- **전자상거래**: 구매 패턴 기반 고객 세그멘테이션으로 맞춤형 상품 추천
- **마케팅**: 인구통계학적 특성 기반 타겟 그룹 식별로 차별화된 캠페인 전략
- **금융서비스**: 거래 행동 패턴 분석을 통한 리스크 그룹 및 상품 타겟팅

## 📝 관련 문제

**Question:** An ecommerce company sends a weekly email newsletter to all of its customers. Management has hired a team of writers to create additional targeted content. A data scientist needs to identify five customer segments based on age, income, and location. The customers' current segmentation is unknown. The data scientist previously built an XGBoost model to predict the likelihood of a customer responding to an email based on age, income, and location. Why does the XGBoost model NOT meet the current requirements, and how can this be fixed?

**Options:**

- A) The XGBoost model provides a true/false binary output. Apply principal component analysis (PCA) with five feature dimensions to predict a segment.
- B) The XGBoost model provides a true/false binary output. Increase the number of classes the XGBoost model predicts to five classes to predict a segment.
- C) The XGBoost model is a supervised machine learning algorithm. Train a k-Nearest-Neighbors (kNN) model with K = 5 on the same dataset to predict a segment.
- D) The XGBoost model is a supervised machine learning algorithm. Train a k-means model with K = 5 on the same dataset to predict a segment.

**정답: D) k-means 클러스터링**

💡 추가 설명:

- **A번 PCA** - 차원축소 기법으로 클러스터를 직접 생성하지 않으며, 여전히 세그먼테이션 문제를 해결하지 못함
- **B번 XGBoost 다중분류** - 지도학습이므로 각 세그먼트의 정답 라벨이 필요한데, 현재 세그먼트가 unknown 상태라 불가능
- **C번 kNN** - 역시 지도학습 알고리즘으로 정답 라벨이 필요하여 현재 상황에 부적합