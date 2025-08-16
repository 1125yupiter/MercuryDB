---

title: categorical-encoding-onehot-binary
created: 2025-08-16
modified: 2025-08-16
tags:
- method/one-hot-encoding
- data-preprocessing/categorical
- ml-fundamentals/encoding
- technique/binarization
- usecase/publishing-prediction
aliases: ["categorical-encoding", "onehot", "binary-conversion"]

---

# 범주형 데이터 바이너리 변환을 위한 One-hot Encoding

## 🎯 핵심 포인트

범주형 데이터를 머신러닝 모델에서 활용하기 위해 바이너리 값으로 변환해야 하는 경우, One-hot encoding을 통해 각 범주를 독립적인 바이너리 벡터로 표현할 수 있다.

## 📝 설명

### One-hot Encoding이 범주형 데이터 바이너리 변환에 적합한 이유

One-hot encoding은 범주형 데이터를 바이너리 형태로 변환하는 가장 표준적인 방법입니다. 각 범주를 하나의 컬럼으로 분리하고, 해당 범주에 속하면 1, 그렇지 않으면 0으로 표현합니다. 이 방법은 범주 간의 순서나 크기 관계를 제거하여 모델이 잘못된 패턴을 학습하는 것을 방지합니다.

출판사의 책 인기도 예측 모델에서는 장르, 출판사명, 작가 등의 범주형 특성들이 중요한 예측 변수가 됩니다. 이러한 특성들을 바이너리로 변환하면 모델이 각 범주의 영향을 독립적으로 학습할 수 있습니다.

### 아키텍처 플로우

```
원본 데이터
    ↓
[장르: "소설", "에세이", "자기계발"]
    ↓
One-hot Encoding 적용
    ↓
장르_소설: [1, 0, 0]
장르_에세이: [0, 1, 0]  
장르_자기계발: [0, 0, 1]
    ↓
머신러닝 모델 훈련
```

### Trade-offs 고려사항

**One-hot Encoding 장점**:
- 범주 간 순서 관계 제거로 올바른 학습 유도
- 각 범주의 독립적 영향도 측정 가능
- 대부분의 ML 알고리즘과 호환성 우수
- 해석이 직관적이고 명확함

**One-hot Encoding 단점**:
- 고유 범주 수만큼 차원 증가 (차원의 저주)
- 희소 행렬(sparse matrix) 생성으로 메모리 사용량 증가
- 새로운 범주 등장 시 재훈련 필요

**Label Encoding 장점**:
- 차원 증가 없이 단일 컬럼 유지
- 메모리 효율적

**Label Encoding 단점**:
- 순서 관계가 없는 범주에 인위적 순서 부여
- 모델이 잘못된 패턴 학습 가능성

## 🔍 주요개념

### 인코딩 방법 비교

- **One-hot Encoding**: 범주형 → 바이너리 벡터 (예: [1,0,0], [0,1,0])
- **Label Encoding**: 범주형 → 정수 (예: 0, 1, 2, 3)
- **Target Encoding**: 범주형 → 타겟 변수 평균값
- **Tokenization**: 텍스트 → 토큰 단위 분할 (NLP 전용)

### 실전 적용

- **출판업계**: 장르, 출판사, 작가 정보를 바이너리로 변환하여 베스트셀러 예측
- **전자상거래**: 상품 카테고리, 브랜드를 인코딩하여 판매량 예측 모델 구축
- **고객 분석**: 직업, 거주지역, 취미 등을 바이너리화하여 고객 세분화 수행

## 📝 관련 문제

**Question:** A publishing company is working on a model that determines how popular a book will be before its release date. A Machine Learning Specialist working on this project wants to convert a column in the training data into binary values first before training the model. Which method should the Specialist apply?

**Options:**

- A) Label encoding
- B) Tokenization  
- C) One-hot encoding
- D) Target encoding

**정답: C) One-hot encoding**

💡 추가 설명:

- **Label encoding** - 범주를 정수로 변환하지만 바이너리 값이 아니며, 순서 관계 없는 범주에 인위적 순서를 부여하는 문제
- **Tokenization** - 자연어처리에서 텍스트를 토큰으로 분할하는 방법으로 범주형 데이터 바이너리 변환과 무관
- **Target encoding** - 범주를 타겟 변수의 평균값으로 대체하는 방법으로 바이너리 변환이 아님