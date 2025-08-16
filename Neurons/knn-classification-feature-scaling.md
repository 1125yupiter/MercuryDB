---

title: knn-classification-feature-scaling
created: 2025-08-16
modified: 2025-08-16
tags:
- algorithm/knn
- problem/classification
- technique/feature-scaling
- issue/low-precision
- preprocessing/normalization
aliases: ["knn-scaling", "feature-normalization", "knn-preprocessing"]

---

# k-NN 이진 분류에서 피처 스케일링이 성능에 미치는 영향

## 🎯 핵심 포인트

수치적 피처들의 스케일 차이가 클 경우 k-NN 분류 모델에서 유클리드 거리 계산이 왜곡되어 정확도와 정밀도가 크게 저하될 수 있다.

## 📝 설명

### k-NN이 온라인 쇼핑 구매 의사 예측에 적합한 이유

k-NN은 거리 기반 알고리즘으로, 새로운 데이터 포인트의 k개 최근접 이웃을 찾아 분류를 수행합니다. 고객의 구매 패턴은 유사한 특성을 가진 다른 고객들의 행동과 연관성이 높기 때문에, k-NN의 "유사한 고객은 유사한 행동을 한다"는 가정이 잘 맞습니다.

하지만 k-NN은 **유클리드 거리**를 사용하여 유사성을 측정하기 때문에, 피처들의 스케일이 거리 계산에 직접적인 영향을 미칩니다. 예를 들어 '나이'(18-35 범위)와 '상품 가격'($50-$5,000 범위)이 함께 사용될 때, 가격의 수치가 나이보다 압도적으로 크기 때문에 거리 계산에서 가격만 고려되고 나이는 무시되는 현상이 발생합니다.

### 문제 발생 메커니즘

```
거리 계산 예시:
고객 A: [나이: 25, 가격: $1,000]
고객 B: [나이: 35, 가격: $2,000]

유클리드 거리 = √((25-35)² + (1000-2000)²) 
              = √(100 + 1,000,000) 
              ≈ 1,000

→ 나이 차이(10)는 거리 계산에서 무시됨
→ 가격 차이(1,000)만 반영됨
```

### 해결방안: 피처 스케일링

**Min-Max Normalization (정규화)**:
```
정규화된 값 = (원본값 - 최솟값) / (최댓값 - 최솟값)

나이: 18~35 → 0~1
가격: $50~$5,000 → 0~1
```

**Z-Score Standardization (표준화)**:
```
표준화된 값 = (원본값 - 평균) / 표준편차

모든 피처가 평균 0, 표준편차 1의 분포를 가짐
```

## 🔍 주요개념

### 성능 저하 요인 비교

- **피처 스케일 차이**: 거리 계산 메커니즘 자체를 왜곡시켜 가장 직접적이고 심각한 영향
- **높은 상관관계**: 중복된 정보로 인한 편향, 고차원에서 더 문제가 됨
- **독립변수 부족**: 예측에 필요한 정보 자체의 부족, 문제 복잡성에 따라 영향 정도가 다름

### 실전 적용

- **전자상거래**: 나이, 소득, 구매 금액, 브라우징 시간 등 다양한 스케일의 피처 정규화
- **금융 서비스**: 연령, 연봉, 대출 금액, 신용 점수 등의 스케일 통일
- **의료 진단**: 나이, 혈압, 콜레스테롤 수치, 혈당 수치 등의 정규화

## 📝 관련 문제

**Question:** A Machine Learning Specialist wants to predict customers' purchasing intentions for an online shop. To build a proof of concept, the Specialist trains a k-NN binary classification model using a low dimensional dataset. The Scientist has tried different hyperparameter tunings, but the model still produces low precision and accuracy scores. What is the MOST likely reason behind this?

**Options:**

- A) The k parameter used to train the model is too low
- B) The training data contains numerical features that have wide-ranging differences
- C) The training data contains a lot of highly correlated values
- D) The training data has a small number of independent variables

**정답: B) The training data contains numerical features that have wide-ranging differences**

💡 추가 설명:

- **A (k 파라미터가 너무 낮다)** - 이미 다양한 하이퍼파라미터 튜닝을 시도했다고 명시되어 있어 가능성 낮음
- **C (높은 상관관계 값들)** - 저차원 데이터셋에서는 상관관계 문제가 상대적으로 덜 심각함
- **D (독립변수 부족)** - Iris 데이터셋처럼 4개 피처로도 높은 정확도 달성 가능하므로, 단순히 변수 개수가 문제는 아님

---