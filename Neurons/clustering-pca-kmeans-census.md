---

title: clustering-pca-kmeans-census
created: 2025-08-17 
modified: 2025-08-17 
tags:
- algorithm/pca
- algorithm/kmeans  
- problem/clustering
- data/census
- usecase/campaign-targeting
aliases: ["PCA-Kmeans", "census-clustering", "demographic-segmentation"]

---

# 인구통계 데이터 클러스터링을 위한 PCA와 K-means 조합

## 🎯 핵심 포인트

인구통계 데이터에서 도시별 의료/사회 프로그램 필요도를 분석하는 경우, 고차원 데이터에서 PCA로 차원을 축소하고 K-means로 클러스터링하여 타겟 캠페인을 설계할 수 있다.

## 📝 설명

### PCA + K-means가 인구통계 분석에 적합한 이유

**Principal Component Analysis (PCA)**는 고차원의 인구통계 데이터에서 중복되거나 유사한 정보를 제거하고 핵심 성분만 추출하는 비지도 학습 알고리즘입니다. 인구조사 데이터는 수백 개의 변수를 포함할 수 있으며, 이 중 상당수는 서로 상관관계가 높아 차원 축소가 필요합니다.

**K-means 클러스터링**은 축소된 특성 공간에서 유사한 특성을 가진 도시들을 그룹화합니다. 각 클러스터는 비슷한 의료/사회적 필요를 가진 도시들의 집합으로, 맞춤형 캠페인 설계의 기초가 됩니다.

### 아키텍처 플로우

```
원시 인구통계 데이터 (고차원)
    ↓
데이터 전처리 및 정규화
    ↓
PCA 차원 축소 (주성분 추출)
    ↓
K-means 클러스터링 (도시 그룹화)
    ↓
클러스터별 특성 분석
    ↓
타겟 캠페인 설계
```

### Trade-offs 고려사항

**PCA 장점**:
- 차원의 저주 문제 해결
- 계산 복잡도 감소 및 성능 향상
- 노이즈 제거 효과
- 시각화 가능한 차원으로 축소

**PCA 단점**:
- 원본 특성의 해석력 손실
- 일부 정보 손실 불가피
- 주성분의 의미 파악 어려움
- 선형 변환만 가능

**K-means 장점**:
- 구현이 간단하고 계산 효율적
- 명확한 클러스터 할당
- 대용량 데이터 처리 가능
- 결과 해석이 직관적

**K-means 단점**:
- 클러스터 수(K)를 사전에 지정해야 함
- 구형 클러스터 가정
- 이상치에 민감
- 초기값에 따른 결과 변동

**대안 서비스들의 한계**:
- **Factorization Machine**: 지도학습 알고리즘으로 라벨 없는 클러스터링에 부적합
- **LDA**: 텍스트 데이터의 토픽 모델링 전용, 수치형 인구통계 데이터에 부적절
- **Random Cut Forest**: 이상치 탐지 목적, 정상적인 그룹화에는 오버스펙

## 🔍 주요개념

### 비지도 학습 vs 지도 학습

- **비지도 학습 (PCA, K-means)**: 라벨 없는 데이터에서 숨겨진 패턴 발견
- **지도 학습 (FM)**: 기존 라벨-결과 쌍을 학습하여 예측 모델 구축

### 클러스터링 vs 분류

- **클러스터링**: 데이터의 자연스러운 그룹 발견 (탐색적 분석)
- **분류**: 미리 정의된 카테고리로 새로운 데이터 할당 (예측적 분석)

### 실전 적용

- **지역별 의료 자원 배치**: 클러스터별 질병 패턴에 따른 병원 특화 전략
- **사회 복지 프로그램 설계**: 소득/연령 분포가 유사한 도시군별 맞춤 지원
- **교육 정책 수립**: 인구 구조가 비슷한 지역의 공통 교육 과제 해결

## 📝 관련 문제

**Question:** A Machine Learning Specialist needs to analyze census data curated from online surveys to determine the healthcare and social program needs per city. The extracted information will be used to create targeted campaigns for different cities in Asia. Which machine learning algorithms should the Specialist use to gain actionable insights?

**Options:**

- A) Principal Component Analysis (PCA) algorithm and K-means algorithm
- B) Factorization machines (FM) algorithm and Latent Dirichlet Allocation (LDA) algorithm  
- C) Principal Component Analysis (PCA) algorithm and Latent Dirichlet Allocation (LDA) algorithm
- D) Factorization machines (FM) algorithm and Random Cut Forest (RCF) algorithm

**정답: A) Principal Component Analysis (PCA) algorithm and K-means algorithm**

💡 추가 설명:

- **옵션 B, D (Factorization Machine)** - 지도학습 알고리즘으로 회귀/분류 문제용. 라벨이 없는 탐색적 클러스터링에는 부적합
- **옵션 B, C (LDA)** - 텍스트 문서의 토픽 발견 전용. 수치형 인구통계 데이터 분석에는 설계 목적 자체가 다름  
- **옵션 D (Random Cut Forest)** - 이상치 탐지 알고리즘. 정상적인 도시 그룹화가 아닌 특이한 도시 찾기에 특화
- **핵심 키워드**: "도시별 그룹화 → 타겟 캠페인" = 전형적인 클러스터링 문제 시나리오