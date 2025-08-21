---

title: categorical-encoding-onehot-preprocessing
created: 2025-08-17
modified: 2025-08-17
tags:
- technique/one-hot-encoding
- problem/feature-engineering
- constraint/categorical-data
- method/supervised-learning
- usecase/loan-approval
aliases: ["one-hot", "categorical-encoding", "feature-transform"]

---

# Categorical Feature Engineering을 위한 One-hot Encoding

## 🎯 핵심 포인트

Categorical features를 numerical values로 변환해야 하는 경우 머신러닝 모델에서, One-hot encoding을 사용할 수 있다

## 📝 설명

### One-hot Encoding이 Categorical Feature Engineering에 적합한 이유

One-hot encoding은 categorical 변수를 머신러닝 알고리즘이 이해할 수 있는 numerical representation으로 변환하는 핵심 기법입니다. 각 고유한 범주마다 별도의 binary column (0 또는 1)을 생성하여, 원본 categorical 정보를 손실 없이 보존하면서 수치형 데이터로 변환합니다.

개인 대출 승인 예측과 같은 classification 문제에서는 직업, 교육수준, 결혼상태 등의 categorical features가 중요한 예측 변수로 작용하며, 이들을 적절히 encoding하는 것이 모델 성능에 직접적인 영향을 미칩니다.

### 아키텍처 플로우

```
원본 Categorical Data
         ↓
[직업: "학생", "회사원", "자영업"]
         ↓
    One-hot Encoding
         ↓
직업_학생: [1, 0, 0]
직업_회사원: [0, 1, 0] 
직업_자영업: [0, 0, 1]
         ↓
ML Algorithm Input (Numerical)
```

### Trade-offs 고려사항

**One-hot Encoding 장점**:
- 정보 손실 없는 완전한 변환
- 범주 간 순서 관계를 가정하지 않음 (nominal data에 적합)
- 대부분의 ML 알고리즘과 호환성 우수
- 구현이 간단하고 직관적

**One-hot Encoding 단점**:
- 고유값이 많은 categorical feature의 경우 차원 급증 (curse of dimensionality)
- sparse matrix 생성으로 메모리 효율성 저하
- 새로운 범주 값 처리 어려움

**Feature Scaling 장점**:
- 이미 numerical인 features의 범위 정규화
- 거리 기반 알고리즘 성능 향상

**Feature Scaling 단점**:
- Categorical data에는 직접 적용 불가
- 범주형 정보를 numerical로 변환하는 기능 없음

## 🔍 주요개념

### Encoding 방법 비교

- **One-hot Encoding**: 각 범주를 별도 binary column으로 변환, 정보 손실 없음
- **Label Encoding**: 범주를 정수로 mapping, 순서 관계 암시 위험
- **Target Encoding**: 범주별 target 평균값 사용, overfitting 위험
- **Binary Encoding**: 범주를 이진수로 변환, 차원 감소 효과

### 실전 적용

- **개인 대출 승인**: 직업, 교육수준, 결혼상태 등을 numerical로 변환
- **고객 분류 모델**: 지역, 연령대, 소득구간 등의 범주형 변수 처리
- **제품 추천 시스템**: 카테고리, 브랜드, 가격대 등의 categorical features 변환

## 📝 관련 문제

**Question:** A Machine Learning Specialist is preparing a dataset to train a predictive model for personal loan approval. The dataset contains numerical, categorical, and ordinal features. To increase prediction accuracy, the Specialist first analyzes the dataset and determines that categorical features need to be transformed into numerical values. Which method of feature engineering is the MOST suitable for this task?

**Options:**

- A) Date formatting
- B) Feature Scaling  
- C) Dimensionality Reduction
- D) One-hot encoding
- E) Data normalization

**정답: D) One-hot encoding**

💡 추가 설명:

- **Date formatting** - 날짜 형식 변환만 수행하며, categorical data 처리와 무관
- **Feature Scaling** - 이미 numerical인 features의 범위 정규화 용도로, categorical → numerical 변환 기능 없음  
- **Dimensionality Reduction** - 기존 features 간 상관관계를 이용한 차원 축소 기법으로, categorical encoding과는 다른 목적
- **Data normalization** - numerical data의 분포 조정 기법으로, categorical 변환 기능 제공하지 않음