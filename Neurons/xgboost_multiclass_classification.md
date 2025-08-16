---
title: classification-xgboost-multiclass
created: 2025-08-16
modified: 2025-08-16
tags:
  - problem/classification
  - algorithm/xgboost
  - technique/multiclass
  - objective/softmax
  - feature/numerical
aliases:
  - xgboost-multiclass
  - multi-softmax
  - xgb-classification
---

# XGBoost 다중 분류를 위한 Objective 설정

## 🎯 핵심 포인트

전자상거래 제품 분류와 같은 다중 분류 문제에서 수치형 피처를 활용할 경우, XGBoost의 `multi:softmax` objective로 높은 성능의 분류 모델을 구현할 수 있다.

## 📝 설명

### XGBoost가 다중 분류에 적합한 이유

XGBoost는 Gradient Boosting 기반의 앙상블 알고리즘으로, 수치형 데이터에 특히 강한 성능을 보입니다. 전자상거거래 제품 분류 시나리오(5,000개 제품, 10개 카테고리, 20개 수치형 피처)에서 다음과 같은 이유로 최적의 선택입니다:

- **수치형 데이터 처리 최적화**: 제품코드, 단가, 차원 등의 수치형 피처를 효과적으로 처리
- **과적합 방지**: 내장된 정규화 기능으로 안정적인 성능 보장
- **하이퍼파라미터 튜닝**: 다양한 조정 옵션으로 성능 최적화 가능
- **빠른 학습 속도**: 대용량 데이터셋에서도 효율적인 학습

### XGBoost Objective 패턴

```
[문제유형]:[처리방식]

multi:softmax     → 다중분류:소프트맥스(클래스 반환)
multi:softprob    → 다중분류:소프트맥스(확률 반환)  
binary:logistic   → 이진분류:로지스틱함수
reg:squarederror  → 회귀:제곱오차
```

### Trade-offs 고려사항

**XGBoost (multi:softmax) 장점**:
- 수치형 데이터에 뛰어난 성능
- 과적합 방지 기능 내장
- 빠른 학습 및 예측 속도
- 해석 가능한 피처 중요도 제공

**XGBoost 단점**:
- 텍스트나 이미지 데이터에는 부적합
- 하이퍼파라미터 튜닝 필요
- 메모리 사용량이 상대적으로 높음

**K-means 단점**:
- 비지도학습으로 라벨 정보 활용 불가
- 제품 분류에 부적합한 클러스터링 방식

**CNN 단점**:
- 수치형 피처에는 오버엔지니어링
- 이미지 데이터가 아닌 경우 비효율적

**NTM 단점**:
- 텍스트 토픽 모델링 전용
- 제품 분류 목적과 불일치

## 🔍 주요개념

### Objective 설정 비교

- **multi:softmax**: 각 샘플에 대해 하나의 클래스 레이블만 반환 (예: `[2, 0, 5, 1]`)
- **multi:softprob**: 각 샘플에 대해 모든 클래스의 확률 반환 (예: `[[0.1, 0.7, 0.2], [0.6, 0.2, 0.2]]`)

### 실전 적용

- **전자상거래 제품 분류**: 제품코드, 가격, 차원 등으로 카테고리 분류
- **고객 세그멘테이션**: 구매 이력, 인구통계로 고객 그룹 분류  
- **금융 위험도 평가**: 재무 지표로 신용 등급 분류

## 📝 관련 문제

**Question:** A Machine Learning Specialist working for an e-commerce store needs to develop a supervised model for product categorization. The Specialist was given a CSV dataset for 5,000 products. Each product is associated with a target label representing its category (e.g., Electronics, Automotive, Office supplies, Clothing). There are a total of 10 categories and 20 numerical features that correspond to variables such as product codes, price, dimensions, and so on. Based on the given dataset, which machine learning approach should the Specialist implement?

**Options:**

- A) Train the model using the built-in K-means algorithm on Amazon SageMaker. Cluster the items into 10 groups by setting the k-parameter to 10.
- B) Use the Amazon SageMaker XGBoost algorithm that has the objective hyperparameter set to multi:softmax.
- C) Train the model using a Keras Convolutional Neural Network (CNN) on Amazon SageMaker.
- D) Use the Amazon SageMaker Neural Topic Model (NTM) algorithm and set the feature_dim hyperparameter to 2,000.

**정답: B) Use the Amazon SageMaker XGBoost algorithm that has the objective hyperparameter set to multi:softmax.**

💡 추가 설명:

- **Option A (K-means)** - 비지도학습 알고리즘으로 라벨 정보를 활용할 수 없어 지도학습 문제에 부적합
- **Option C (CNN)** - 이미지 데이터 처리에 특화된 모델로 수치형 피처 분류에는 오버엔지니어링
- **Option D (NTM)** - 텍스트 토픽 모델링 알고리즘으로 제품 분류 목적과 불일치

### 설정 예시

```python
# XGBoost 다중 분류 설정
xgb_params = {
    'objective': 'multi:softmax',  # 다중 분류
    'num_class': 10,              # 10개 카테고리
    'max_depth': 6,
    'eta': 0.3,
    'subsample': 0.8
}

# 확률값이 필요한 경우
xgb_params_prob = {
    'objective': 'multi:softprob',  # 확률 반환
    'num_class': 10,
    'max_depth': 6,
    'eta': 0.3
}
```