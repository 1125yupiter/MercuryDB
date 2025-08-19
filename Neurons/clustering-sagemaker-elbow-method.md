---

title: clustering-sagemaker-elbow-method
created: 2025-08-19
modified: 2025-08-19
tags:
- problem/clustering
- service/sagemaker
- method/elbow-method
- usecase/customer-segmentation
- technique/wss-analysis
aliases: ["k-means", "elbow-method", "customer-segmentation"]

---

# K-Means 클러스터링에서 최적 k값 선택을 위한 Elbow Method 활용

## 🎯 핵심 포인트

리테일 고객 세그멘테이션을 위한 k-평균 클러스터링을 수행하는 경우 Amazon SageMaker에서 Elbow Method를 사용하여 WSS(Within-cluster Sum of Squares) 분석으로 최적의 클러스터 수를 효율적으로 결정할 수 있다.

## 📝 설명

### Elbow Method가 클러스터링 최적화에 적합한 이유

Elbow Method는 k-평균 클러스터링에서 가장 널리 사용되는 k값 결정 방법입니다. WSS(클러스터 내 제곱합)을 서로 다른 k값에 대해 계산하고, 이를 플롯하여 "팔꿈치" 형태로 꺾이는 지점을 찾아 최적 k를 결정합니다. Amazon SageMaker의 분산 처리 환경을 활용하면 여러 k값에 대한 모델을 병렬로 학습시켜 효율적으로 최적값을 찾을 수 있습니다.

### 아키텍처 플로우

```
고객 데이터 (S3) → 전처리 (SageMaker) → k=2~10 모델 병렬 학습
                                              ↓
WSS 계산 및 저장 ← 각 k별 k-means 모델 ← 학습 완료
    ↓
Elbow Point 식별 → 최적 k 선택 → 최종 클러스터링 모델 배포
```

### Trade-offs 고려사항

**Elbow Method 장점**:
- 직관적이고 시각적으로 해석하기 쉬움
- 계산 복잡도가 상대적으로 낮음
- SageMaker에서 병렬 처리로 빠른 실행 가능
- 비용 효율적인 접근법

**Elbow Method 단점**:
- 주관적 판단이 필요한 경우가 있음
- 명확한 "팔꿈치" 지점이 없을 수 있음
- 데이터 분포에 따라 결과가 달라질 수 있음

**Gap Statistic 장점**:
- 통계적으로 더 정교한 방법
- 객관적인 k값 결정 가능

**Gap Statistic 단점**:
- 계산 비용이 높음
- 복잡한 구현이 필요
- 클라우드 환경에서 비용 부담 증가

## 🔍 주요개념

### 클러스터 평가 지표 비교

- **WSS (Within-cluster Sum of Squares)**: 각 클러스터 내 데이터 포인트들과 중심점 간의 거리 제곱합
- **Silhouette Score**: 클러스터 내 응집도와 클러스터 간 분리도를 종합적으로 평가
- **Gap Statistic**: 실제 클러스터링 결과와 랜덤 분포 기준선을 비교한 통계적 지표

### 실전 적용

- 리테일 고객을 구매 빈도, 금액, 최근성(RFM)으로 세그멘테이션
- 전자상거래 사용자 행동 패턴 분석으로 개인화 추천 그룹 생성
- 제품 카테고리별 판매 데이터 클러스터링으로 재고 관리 최적화

## 📝 관련 문제

**Question:** A data scientist at a retail company is analyzing customer purchase patterns to segment them into distinct groups for targeted marketing campaigns. To achieve this, the scientist is employing k-Means clustering. What is the most effective method for selecting the optimal number of clusters (k) to accurately categorize customers into meaningful segments?

**Options:**

- A) Apply Principal Component Analysis (PCA) using Amazon Redshift ML to automatically select the best k value based on data dimensionality reduction
- B) Implement the silhouette analysis using AWS Glue DataBrew for data preparation and feature engineering before clustering
- C) Apply the "elbow method" by analyzing a plot of the total within-cluster sum of squares (WSS) against the number of clusters (k)
- D) Utilize AWS Lambda with Amazon QuickSight for dynamic scaling and computing the Gap Statistic to find the optimal number of clusters

**정답: C) Apply the "elbow method" by analyzing a plot of the total within-cluster sum of squares (WSS) against the number of clusters (k)**

💡 추가 설명:

- **Option A** - PCA는 차원 축소에 유용하지만 k값을 직접 결정하지는 않으며, Redshift ML은 클러스터링 최적화에 특화되지 않음
- **Option B** - Silhouette 분석은 유효하지만 DataBrew는 데이터 준비 도구로 클러스터 수 결정에 직접적 도움을 주지 않음
- **Option D** - Gap Statistic은 통계적으로 정교하지만 계산 비용이 높고, Lambda와 QuickSight는 이 작업에 최적화되지 않음

---