---

title: tree-ensemble-hyperparameters
created: 2025-01-27
modified: 2025-01-27
tags:
- algorithm/tree-ensemble
- technique/hyperparameter
- method/supervised
- algorithm/randomforest
- algorithm/xgboost
aliases: ["tree-hyperparams", "ensemble-tuning", "tree-params"]

---

# 트리 기반 앙상블 모델 하이퍼파라미터 완전 가이드

## 🎯 핵심 포인트

트리 기반 앙상블 모델을 구성하는 경우 개별 트리의 형태와 앙상블 구조를 제어하는 하이퍼파라미터를 통해 모델의 복잡도, 성능, 훈련 시간을 조절할 수 있다.

## 📝 설명

### 트리 기반 앙상블 하이퍼파라미터의 구조적 특징

트리 기반 앙상블 모델의 하이퍼파라미터는 개별 트리의 구조를 결정하는 설정값들입니다. 이들은 모델이 데이터로부터 학습하는 내용(분할 기준값, 예측값)과는 구별되며, 사용자가 사전에 설정하여 트리의 "모양"과 "생성 방식"을 제어합니다.

하이퍼파라미터는 크게 세 가지 범주로 분류됩니다: (1) 개별 트리 구조 제어, (2) 앙상블 구성 제어, (3) 샘플링 및 정규화 제어. 각 범주의 하이퍼파라미터들은 서로 상호작용하며 최종 모델의 성능에 영향을 미칩니다.

### 하이퍼파라미터 vs 학습된 파라미터

```
하이퍼파라미터 (사용자 설정):
├── max_depth = 3         # "3층까지만 만들어라"
├── min_samples_split = 10 # "10개 미만이면 분할 금지"
└── n_estimators = 100    # "트리 100개 만들어라"

학습된 파라미터 (모델이 데이터에서 발견):
├── Root: age > 25.5      # 모델이 25.5를 최적값으로 발견
├── Left: income > 45000  # 모델이 45000을 최적값으로 발견  
└── Right: education > 12 # 모델이 12를 최적값으로 발견
```

### 개별 트리 구조 제어 하이퍼파라미터

**max_depth (최대 트리 깊이)**:
- 트리의 최대 층수를 제한하여 모델 복잡도를 직접 제어
- 깊이 증가 시 노드 수가 지수적으로 증가 (2^depth)
- 너무 깊으면 오버피팅, 너무 얕으면 언더피팅

**min_samples_split**:
- 노드를 분할하기 위해 필요한 최소 샘플 수
- 높을수록 보수적인 분할로 오버피팅 방지
- 너무 높으면 중요한 패턴을 놓칠 수 있음

**min_samples_leaf**:
- 리프 노드가 가져야 할 최소 샘플 수
- 모델 예측의 안정성을 보장
- 너무 높으면 트리가 너무 단순해짐

**max_features**:
- 각 분할에서 고려할 최대 특성 수
- 'sqrt', 'log2', 또는 정수값으로 설정
- 트리 간 다양성 증대로 앙상블 성능 향상

### 앙상블 구성 제어 하이퍼파라미터

**n_estimators**:
- 생성할 트리의 총 개수
- 많을수록 성능 향상되지만 훈련 시간 증가
- 수확체감의 법칙 적용 (100→200보다 200→300의 개선폭이 작음)

**learning_rate** (Gradient Boosting 계열):
- 각 트리의 기여도를 조절하는 가중치
- 낮을수록 천천히 학습하지만 더 정확
- n_estimators와 반비례 관계 (낮은 learning_rate = 많은 트리 필요)

### 실제 하이퍼파라미터 상호작용 패턴

```
깊이-트리수 관계:
├── 깊은 트리(15+) + 적은 개수(50) = 복잡하지만 다양성 부족
├── 얕은 트리(3-5) + 많은 개수(500) = 단순하지만 다양한 조합
└── 중간 깊이(8-12) + 적당한 개수(100-300) = 균형잡힌 성능

학습률-트리수 관계 (Boosting):
├── 낮은 학습률(0.01) + 많은 트리(1000) = 천천히 정확하게
├── 높은 학습률(0.3) + 적은 트리(100) = 빠르지만 불안정
└── 중간 학습률(0.1) + 적당한 트리(300) = 실용적 균형
```

## 🔍 주요개념

### 알고리즘별 핵심 하이퍼파라미터

- **Random Forest**: n_estimators, max_depth, max_features, bootstrap
- **XGBoost**: n_estimators, max_depth, learning_rate, subsample, colsample_bytree
- **LightGBM**: n_estimators, max_depth, learning_rate, num_leaves, feature_fraction
- **CatBoost**: iterations, depth, learning_rate, l2_leaf_reg

### 하이퍼파라미터 튜닝 전략

**Grid Search 예시**:
```python
param_grid = {
    'max_depth': [5, 10, 15],
    'n_estimators': [100, 200, 300],
    'min_samples_split': [2, 5, 10]
}
# 총 27가지 조합 (3×3×3)
```

**Random Search 예시**:
```python
param_distributions = {
    'max_depth': randint(3, 20),
    'n_estimators': randint(50, 500),
    'learning_rate': uniform(0.01, 0.3)
}
# 무작위로 100개 조합 샘플링
```

### 실전 적용

- **고차원 데이터**: max_features='sqrt'로 특성 부분집합 사용하여 다양성 확보
- **불균형 데이터**: class_weight 조정으로 소수 클래스에 더 높은 가중치
- **실시간 예측**: max_depth 제한으로 예측 속도 향상, 메모리 사용량 감소

## 📝 관련 문제

**Question:** A data scientist is tuning hyperparameters for a Random Forest classifier on a dataset with 50,000 samples and 200 features. The current model shows signs of overfitting with training accuracy of 99% and validation accuracy of 75%. Which combination of hyperparameter adjustments would be most effective in reducing overfitting?

**Options:**

- A) Increase max_depth and increase n_estimators
- B) Decrease max_depth and increase min_samples_split  
- C) Increase max_features and decrease min_samples_leaf
- D) Decrease n_estimators and increase max_features
- E) Increase learning_rate and decrease max_depth

**정답: B) Decrease max_depth and increase min_samples_split**

💡 추가 설명:

- **Option A** - max_depth 증가는 오버피팅을 악화시킴, 더 복잡한 트리 생성
- **Option B** - 두 파라미터 모두 모델 복잡도를 감소시켜 오버피팅 완화에 효과적
- **Option C** - max_features 증가와 min_samples_leaf 감소 모두 오버피팅 유발
- **Option D** - n_estimators 감소는 앙상블 성능을 떨어뜨릴 수 있음
- **Option E** - learning_rate는 Random Forest에 적용되지 않는 하이퍼파라미터