---

title: missing-data-clustering-handling
created: 2025-08-25
modified: 2025-08-25
tags:
- technique/clustering
- problem/missing-data
- method/imputation
- algorithm/kmeans
- domain/healthcare
aliases: ["missing-data", "imputation", "clustering-imputation"]

---

# 결측 데이터 처리를 위한 클러스터링 기반 접근법

## 🎯 핵심 포인트

중요한 특성에서 결측값이 다량 발생한 경우, 다른 특성들을 기반으로 클러스터링을 수행하여 유사한 패턴의 샘플들로 그룹화한 후 해당 그룹 내의 통계값으로 결측값을 대체할 수 있다.

## 📝 설명

### 클러스터링 기반 결측값 처리가 적합한 이유

결측값 처리에서 클러스터링 접근법은 단순한 통계적 대체보다 데이터의 내재된 패턴을 활용한다는 점에서 우수합니다. 특히 의료 데이터처럼 개체 간 유사성이 중요한 도메인에서 효과적입니다. K-means 클러스터링을 활용하면 결측값이 없는 다른 특성들을 기반으로 유사한 환자군을 식별하고, 각 그룹의 특성에 맞는 개인화된 추정이 가능합니다.

### 아키텍처 플로우

```
원본 데이터 (4,000 samples)
    ↓
결측값 확인 (450개 age=0)
    ↓
결측값이 없는 특성들로 클러스터링
    ↓
각 클러스터별로 결측값 대체
    ↓
완성된 데이터셋으로 모델 훈련
```

### Trade-offs 고려사항

**클러스터링 기반 대체 장점**:
- 개별 샘플의 특성을 고려한 개인화된 추정
- 데이터의 전체적인 구조와 패턴 보존
- 단순 통계적 대체보다 현실적인 값 생성
- 전체 데이터셋 활용 가능

**클러스터링 기반 대체 단점**:
- 계산 복잡도가 높음
- 클러스터 수(k) 결정이 어려움
- 다른 특성들도 결측이 많으면 효과 제한적

**단순 삭제(A번) 장점**:
- 구현이 간단하고 빠름
- 명확한 데이터 품질 기준

**단순 삭제(A번) 단점**:
- 11.25% 데이터 손실로 인한 정보 손실
- 샘플 크기 감소로 모델 성능 저하 가능

## 🔍 주요개념

### 결측값 처리 방법 비교

- **완전 삭제(Listwise Deletion)**: 결측값이 있는 모든 샘플 제거
- **단순 대체(Mean/Median Imputation)**: 전체 평균이나 중간값으로 대체
- **클러스터 기반 대체(Cluster-based Imputation)**: 유사 그룹의 통계값으로 대체
- **다중 대체(Multiple Imputation)**: 여러 가능한 값으로 대체 후 결과 통합

### 실전 적용

- **의료 데이터**: 환자의 나이, 혈압, 검사 결과 등에서 결측값 처리
- **고객 분석**: 고객의 소득, 구매 이력 등에서 결측값 보완
- **센서 데이터**: IoT 센서에서 일시적 장애로 인한 결측값 복구

## 📝 관련 문제

**Question:** A data scientist is constructing a machine learning model to forecast future health outcomes using data about each patient and their treatment plans. The model should anticipate a continuous value. The offered data set contains labeled outcomes for 4,000 patients. The research examined a group of people over the age of 65 who had a specific condition that is known to deteriorate with age. Initial models have been unsatisfactory. While studying the underlying data, the Data Scientist discovers that out of 4,000 patient observations, 450 had an age of 0. The remaining characteristics of these observations seem to be normal in comparison to the remainder of the sample group. What should the Data Scientist do to rectify this situation?

**Options:**

- A) Drop all records from the dataset where age has been set to 0
- B) Replace the age field value for records with a value of 0 with the mean or median value from the dataset
- C) Drop the age feature from the dataset and train the model using the rest of features
- D) Use k-means clustering to handle missing features
- E) Apply data normalization to handle the age outliers

**정답: D) Use k-means clustering to handle missing features**

💡 추가 설명:

- **A번 (완전 삭제)** - 11.25%의 상당한 데이터 손실로 인한 모델 성능 저하 가능성
- **B번 (단순 대체)** - 모든 결측값을 같은 값으로 대체하여 데이터 분산 축소 및 편향 발생
- **C번 (특성 제거)** - 연령 관련 질병 연구에서 가장 중요한 예측 변수인 나이를 제거하는 것은 모순
- **E번 (정규화)** - 나이 0은 이상치가 아닌 명백한 결측값이므로 정규화로는 해결 불가