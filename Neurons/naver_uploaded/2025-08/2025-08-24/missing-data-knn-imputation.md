---

title: missing-data-knn-imputation-ml
created: 2025-08-18 
modified: 2025-08-18 
tags:
- problem/missing-data
- technique/knn-imputation
- constraint/model-accuracy
- method/supervised-learning
- domain/real-estate
aliases: ["missing-values", "imputation", "KNN"]

---

# 결측치 처리에서 KNN 대치법이 효과적인 경우

## 🎯 핵심 포인트

중요한 수치형 특성(집의 면적 등)에 결측치가 약 10% 존재하는 경우, KNN 대치법을 사용하면 데이터 구조와 복잡한 관계를 보존하며 모델 정확도를 향상시킬 수 있다.

## 📝 설명

### KNN 대치법이 결측치 처리에 적합한 이유

KNN(K-Nearest Neighbors) 대치법은 유사한 특성을 가진 이웃 샘플들의 정보를 활용하여 결측치를 추정하는 방법입니다. 특히 집의 면적과 같은 중요한 수치형 변수에서 뛰어난 성능을 보입니다. 이 방법은 데이터 포인트 간의 거리를 계산하여 가장 유사한 K개의 샘플을 찾고, 이들의 값으로부터 결측치를 추정합니다.

### 아키텍처 플로우

```
원본 데이터 → 거리 계산 → K개 이웃 선택 → 가중평균 계산 → 결측치 대체
     ↓              ↓              ↓              ↓              ↓
[결측치 식별]   [유클리드/맨하탄]  [유사도 순위]   [거리 가중치]   [완전한 데이터셋]
     ↓              ↓              ↓              ↓              ↓
특성 정규화 ←  특성 벡터 생성 ←  이웃 후보군 ←  대치값 결정 ←  모델 훈련 준비
```

### Trade-offs 고려사항

**KNN 대치법 장점**:
- 데이터의 국소적 구조와 패턴을 보존
- 비선형 관계와 복잡한 상호작용 포착 가능
- 원본 데이터의 분산과 분포 특성 유지
- 범주형, 수치형 데이터 모두 처리 가능

**KNN 대치법 단점**:
- 계산 비용이 상대적으로 높음
- 차원의 저주에 민감할 수 있음
- K값 선택에 따른 성능 변동
- 특성 스케일링 필요

**평균 대치법 장점**:
- 구현이 간단하고 빠름
- 메모리 사용량이 적음

**평균 대치법 단점**:
- 분산 감소와 편향 도입
- 데이터 분포 왜곡
- 변수 간 상관관계 무시

**선형 회귀 대치법 장점**:
- 다른 특성들과의 관계 활용
- 예측 기반 추정

**선형 회귀 대치법 단점**:
- 선형 관계 가정의 한계
- 복잡한 비선형 패턴 놓침

## 🔍 주요개념

### 대치법 비교

- **KNN 대치법**: 유사한 데이터 포인트들의 값을 이용한 국소적 추정 방법
- **평균 대치법**: 전체 데이터의 평균값으로 단순 대체하는 전역적 방법
- **회귀 대치법**: 다른 변수들을 이용한 예측 모델 기반 추정
- **최빈값 대치법**: 가장 빈번한 값으로 대체 (주로 범주형 데이터용)

### 실전 적용

- 부동산 가격 예측에서 면적, 방 개수, 건축연도 등의 결측치 처리
- 고객 행동 분석에서 구매 이력, 선호도 점수 등의 누락 데이터 보완
- 의료 데이터에서 환자의 생체 지표, 검사 결과 등의 결측치 추정

## 📝 관련 문제

**Question:** A data science team is building a machine learning model to predict house sale prices using features like square footage. About 10% of entries in their training dataset are missing the square footage attribute. Given the importance of model accuracy, which approach should they use to handle missing values effectively?

**Options:**

- A) Replace missing square footage with the dataset's mode
- B) Impute missing square footage using mean imputation before training  
- C) Apply linear regression to estimate missing square footage based on other features
- D) Employ k-nearest neighbors (KNN) imputation to fill missing square footage
- E) Remove all rows with missing square footage values

**정답: D) Employ k-nearest neighbors (KNN) imputation to fill missing square footage**

💡 추가 설명:

- **A) 최빈값 대치법** - 범주형 데이터에 적합하며, 연속형 수치 데이터인 면적에는 부적절함
- **B) 평균 대치법** - 분산을 감소시키고 편향을 도입하여 특히 치우친 분포에서 부정확함  
- **C) 선형 회귀 대치법** - 선형 관계를 가정하나 실제 부동산 데이터는 복잡한 비선형 패턴을 가짐
- **E) 완전 제거법** - 10%의 데이터 손실로 인한 정보 손실과 편향 위험이 큼