---

title: classification-xgboost-multiclass-retail
created: 2025-08-26
modified: 2025-08-26
tags:
- problem/classification
- algorithm/xgboost
- usecase/product-classification
- data/tabular
- technique/multiclass
aliases: ["product-classification", "multi-class", "xgboost-retail"]

---

# 머신러닝을 이용한 상품 분류 - XGBoost Multi-class Classification

## 🎯 핵심 포인트

소매업체가 표 형태의 상품 속성 데이터로 새로운 상품을 자동 분류해야 하는 경우, XGBoost의 multi:softmax 목적함수를 사용하여 다중 클래스 분류를 효과적으로 수행할 수 있다.

## 📝 설명

### XGBoost가 상품 분류에 적합한 이유

XGBoost는 표 형태 데이터(tabular data)에서 뛰어난 성능을 보이는 gradient boosting 알고리즘입니다. 상품 분류 시나리오에서 다음과 같은 특징들이 매우 적합합니다:

- **혼합 데이터 타입 처리**: 제목(텍스트), 크기(수치), 무게(수치), 가격(수치) 등 다양한 속성을 효과적으로 처리
- **Feature Importance**: 어떤 속성이 분류에 중요한지 해석 가능
- **Robust Performance**: 1,200개 정도의 중소규모 데이터셋에서도 안정적인 성능
- **Multi-class Support**: `multi:softmax` 및 `multi:softprob` 목적함수로 다중 클래스 분류 지원

### 분류 예측 결과 형태

**multi:softmax 설정**:
```
입력: 새 상품 속성 [제목, 크기, 무게, 가격, ...]
출력: "게임" (최종 예측 클래스만)
```

**multi:softprob 설정** (실무에서 더 유용):
```
입력: Nintendo Switch 게임 패키지
출력: 
- 게임: 75%
- 가젯: 20%
- 장난감: 4%
- 기타: 1%
```

### 아키텍처 플로우

```
원본 데이터 (1,200개 상품)
    ↓
특성 전처리 (15개 속성)
    ↓
XGBoost 모델 훈련 (multi:softmax)
    ↓
새로운 상품 입력
    ↓
분류 예측 + 확신도
    ↓
자동 카테고리 할당 또는 수동 검토
```

### Trade-offs 고려사항

**XGBoost 장점**:
- 표 형태 데이터에서 검증된 높은 성능
- 빠른 학습 및 예측 속도
- Feature importance로 해석 가능성 제공
- 하이퍼파라미터 튜닝으로 성능 최적화 가능
- 과적합 방지 기능 내장

**XGBoost 단점**:
- 텍스트 데이터(상품 제목)의 의미적 이해 제한
- 매우 큰 데이터셋에서는 메모리 사용량 증가
- 하이퍼파라미터 튜닝 필요

**CNN 장점**:
- 이미지 데이터가 있다면 강력한 성능

**CNN 단점**:
- 표 형태 데이터에는 부적합한 아키텍처
- 공간적 관계가 없는 상품 속성에는 무의미

**회귀 모델 단점**:
- 분류가 아닌 연속값 예측용
- 카테고리 예측에 부적합

**DeepAR 단점**:
- 시계열 예측 전용 모델
- 상품 분류와 무관한 아키텍처

## 🔍 주요개념

### Multi-class Classification 방식 비교

- **multi:softmax**: 최종 예측 클래스만 반환 (예: "게임")
- **multi:softprob**: 각 클래스별 확률 반환 (예: 게임 75%, 가젯 20%)

### 실전 적용

- **확신도 기반 자동화**: 80% 이상 확신할 때만 자동 분류, 그 외는 수동 검토
- **교차 판매 전략**: 게임 75%, 가젯 20%인 상품은 두 카테고리 고객 모두에게 추천
- **애매한 케이스 처리**: 확률이 비슷한 경우(40%, 35%, 25%) 전문가 검토 필요

## 📝 관련 문제

**Question:** A retailer aims to classify new items using machine learning. The Data Science team was presented with a labeled dataset of current goods with 1,200 products, each having 15 attributes including title, dimensions, weight, and price. Each item is tagged with categories like books, games, gadgets, or movies. Which model should be used to classify new items?

**Options:**

- A) An XGBoost model where the objective parameter is set to multi:softmax
- B) A deep convolutional neural network (CNN) with a softmax activation function for the last layer  
- C) A regression forest where the number of trees is set equal to the number of product categories
- D) A DeepAR forecasting model based on a recurrent neural network (RNN)

**정답: A) XGBoost model with multi:softmax objective**

💡 추가 설명:

- **Option B (CNN)** - CNN은 이미지의 공간적 관계를 학습하도록 설계되어, 표 형태의 상품 속성 데이터에는 부적합
- **Option C (Regression Forest)** - 회귀는 연속값 예측용이며, 카테고리 분류에는 부적절. 또한 트리 수를 카테고리 수와 맞출 필요 없음
- **Option D (DeepAR)** - 시계열 예측 모델로 상품 분류와는 전혀 무관한 아키텍처