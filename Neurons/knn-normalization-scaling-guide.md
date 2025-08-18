---

title: knn-normalization-scaling-guide
created: 2025-08-18 
modified: 2025-08-18 
tags:
- algorithm/knn
- preprocessing/normalization
- problem/feature-scaling
- method/supervised
- constraint/distance-based
aliases: ["knn-scaling", "feature-normalization", "distance-scaling"]

---

# K-NN 모델에서 피처 스케일링과 정규화 기법

## 🎯 핵심 포인트

K-NN과 같은 거리 기반 알고리즘에서 피처들의 크기 차이가 클 경우, 정규화 변환(Normalization Transformation)을 통해 모든 피처가 동등하게 거리 계산에 기여할 수 있다.

## 📝 설명

### K-NN이 정규화에 민감한 이유

K-NN 알고리즘은 유클리드 거리를 사용하여 데이터 포인트 간의 유사성을 측정합니다. 거리 계산 시 각 피처의 차이값을 제곱하여 합산하기 때문에, 큰 값을 가진 피처가 거리 계산을 지배하게 됩니다.

**문제 시나리오 예시:**
- 나이: 18-35 (범위: 17)
- 제품 가격: $50-$5,000 (범위: $4,950)

유클리드 거리 = √[(나이차이)² + (가격차이)²]에서 가격 차이가 나이 차이보다 수백 배 크므로, 나이는 사실상 분류에 영향을 주지 못합니다.

### 정규화 변환 방법들

#### 1. Min-Max Normalization (0-1 스케일링)
```
정규화값 = (원본값 - 최솟값) / (최댓값 - 최솟값)
```
- 모든 값을 0~1 범위로 변환
- 이상치에 민감함

#### 2. Z-Score Normalization (표준화)
```
정규화값 = (원본값 - 평균) / 표준편차
```
- 평균 0, 표준편차 1로 변환
- 정규분포 가정

#### 3. Robust Scaling
```
정규화값 = (원본값 - 중앙값) / IQR
```
- 이상치에 강건함
- 중앙값과 사분위수 사용

### 아키텍처 플로우

```
원시 데이터 입력
    ↓
피처별 통계량 계산 (min/max, 평균/표준편차)
    ↓
정규화 변환 적용
    ↓
정규화된 데이터셋
    ↓
K-NN 거리 계산 (유클리드 거리)
    ↓
k개 최근접 이웃 선택
    ↓
다수결/평균을 통한 예측
```

### Trade-offs 고려사항

**Normalization Transformation 장점**:
- 모든 피처가 동등하게 거리 계산에 기여
- K-NN, SVM, Neural Network 등 거리/그래디언트 기반 알고리즘 성능 향상
- 수치적 안정성 개선

**Normalization Transformation 단점**:
- 추가 전처리 단계 필요
- 새로운 데이터에 대해 같은 변환 파라미터 유지 필요
- 이상치가 있을 경우 Min-Max는 효과 제한적

**다른 변환 방법들의 한계**:
- **Quantile Binning**: 연속값을 구간으로 나누지만 스케일 차이 해결 안됨
- **N-gram/OSB**: 텍스트 분석용으로 수치 피처 스케일링과 무관

## 🔍 주요개념

### 거리 기반 vs 트리 기반 알고리즘

- **거리 기반 (K-NN, SVM, K-Means)**: 정규화 필수, 피처 스케일에 민감
- **트리 기반 (Random Forest, XGBoost)**: 정규화 불필요, 분할 기준이 상대적 순서 기반

### 실전 적용

- **고객 세분화**: 나이, 소득, 구매횟수 등 다양한 스케일의 피처 통합
- **추천 시스템**: 사용자 행동 지표들(클릭수, 시간, 평점) 간 균형 조정
- **이상 탐지**: 센서 데이터의 다양한 물리량들을 동일 스케일로 비교

## 📝 관련 문제

**Question:** A Machine Learning Specialist is training a K-NN model that can predict employment rates based on a range of factors such as economic climate, technological advances, seasonal fluctuations, and so on. During data analysis, the Specialist observes different input variables that significantly vary in magnitude. The Specialist wants to avoid having a dataset whose features with a larger value greatly influence the model's predictive capability. Which transformation method should be taken by the ML-Specialist to achieve his goal?

**Options:**

- A) N-gram Transformation
- B) Orthogonal Sparse Bigram (OSB) Transformation  
- C) Normalization Transformation
- D) Quantile Binning Transformation

**정답: C) Normalization Transformation**

💡 추가 설명:

- **N-gram Transformation** - 텍스트를 단어/문자 조합으로 분할하는 기법으로 수치 피처 스케일링과 무관
- **OSB Transformation** - 텍스트 분석용 bi-gram의 대안으로 수치 데이터 정규화 효과 없음
- **Quantile Binning** - 연속값을 구간으로 나누는 기법이지만 피처 간 스케일 차이 해결 안됨
- **Normalization Transformation** - K-NN의 거리 계산에서 모든 피처가 공정하게 기여하도록 스케일 조정