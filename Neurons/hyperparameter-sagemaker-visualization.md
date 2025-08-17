---

title: hyperparameter-sagemaker-visualization
created: 2025-08-17
modified: 2025-08-17 
tags:
- service/sagemaker
- technique/hyperparameter
- visualization/scatter-plot
- algorithm/xgboost
- usecase/classification
aliases: ["HPO", "hyperparameter-tuning", "model-optimization"]

---

# 하이퍼파라미터 튜닝 시각화 - SageMaker XGBoost 분류 모델 최적화

## 🎯 핵심 포인트

SageMaker에서 XGBoost 분류 모델의 하이퍼파라미터와 성능 간 상관관계를 분석하는 경우, AUC-하이퍼파라미터 조합별로 산점도를 사용하여 효과적으로 최적화할 수 있다.

## 📝 설명

### 산점도가 하이퍼파라미터 튜닝에 적합한 이유

SageMaker에서 하이퍼파라미터 튜닝 시 산점도는 두 변수 간의 상관관계를 명확하게 시각화할 수 있는 가장 효과적인 방법입니다. 특히 XGBoost의 eta, alpha, max_depth, min_child_weight와 같은 핵심 하이퍼파라미터와 모델 성능(AUC) 간의 관계를 직관적으로 파악할 수 있습니다.

분류 모델에서는 AUC(Area Under the Curve)가 모델의 성능을 평가하는 표준 지표로 사용되며, 0.5에서 1.0 사이의 값으로 높을수록 우수한 성능을 나타냅니다. 각 하이퍼파라미터 값에 따른 AUC 변화를 산점도로 시각화하면 최적 구간을 쉽게 식별할 수 있습니다.

### 아키텍처 플로우

```
SageMaker Hyperparameter Tuning Job
         ↓
XGBoost Training (Multiple Iterations)
         ↓
Performance Metrics Collection (AUC)
         ↓
Scatter Plot Visualization
(AUC vs eta, alpha, max_depth, min_child_weight)
         ↓
Correlation Analysis
         ↓
Hyperparameter Reconfiguration
         ↓
Optimized Model Deployment
```

### Trade-offs 고려사항

**산점도 시각화 장점**:
- 두 변수 간 직접적인 상관관계 파악 가능
- 최적 하이퍼파라미터 범위 직관적 식별
- 이상치(outlier) 쉽게 발견 가능
- 다양한 하이퍼파라미터 조합 효과 비교 용이

**산점도 시각화 단점**:
- 고차원 하이퍼파라미터 공간 표현에 한계
- 변수 간 복잡한 상호작용 파악 어려움
- 많은 데이터 포인트 시 겹침 현상 발생 가능

**t-SNE + 산점도 조합 단점**:
- t-SNE는 차원 축소용으로 상관관계 시각화 부적절
- 매 반복마다 적용 시 훈련 시간 급증
- 원본 하이퍼파라미터 값과 성능 관계 왜곡 가능

**RMSE 기반 시각화 단점**:
- RMSE는 회귀 모델 평가 지표로 분류 문제에 부적절
- 분류 모델 성능을 정확히 반영하지 못함

## 🔍 주요개념

### 분류 모델 성능 지표 비교

- **AUC (Area Under the Curve)**: ROC 곡선 아래 면적으로 분류 모델의 전반적 성능 측정, 0.5-1.0 범위
- **RMSE (Root Mean Square Error)**: 회귀 모델용 지표로 예측값과 실제값 차이의 제곱근 평균
- **Accuracy**: 전체 예측 중 정확한 예측 비율, 불균형 데이터에서 오해 소지
- **F1-Score**: 정밀도와 재현율의 조화평균, 불균형 클래스 문제에 유용

### 실전 적용

- **고객 이탈 예측**: 각 하이퍼파라미터와 이탈 예측 정확도(AUC) 간 관계 분석으로 최적 모델 구성
- **사기 거래 탐지**: 실시간 사기 탐지 모델의 민감도와 특이도 균형을 위한 하이퍼파라미터 최적화
- **의료 진단 보조**: 질병 진단 정확도 향상을 위한 XGBoost 모델 하이퍼파라미터 세밀 조정

## 📝 관련 문제

**Question:** A Machine Learning Specialist has created a hyperparameter tuning job on an Amazon SageMaker notebook instance. The tuning job will use the built-in XGBoost algorithm to train a classification model. The ML Specialist wants to visualize the correlation of the eta, alpha, max_depth, and min_child_weight hyperparameters with the model's performance at each iteration so she can reconfigure them to attain the best model version. Which visualization technique should the ML Specialist use?

**Options:**

- A) Use a scatter plot to visualize the results for each Area Under the Curve (AUC)-hyperparameter combination
- B) Use a scatter plot with data points colored by the AUC metric and apply t-Distributed Stochastic Neighbor Embedding (t-SNE) to the input variables to generate better data visualizations
- C) Use a histogram to visualize the results and only reconfigure hyperparameters near the mean for subsequent iterations
- D) Use a scatter plot to visualize the results for each root mean square error (RMSE)-hyperparameter combination

**정답: A) Use a scatter plot to visualize the results for each Area Under the Curve (AUC)-hyperparameter combination**

💡 추가 설명:

- **옵션 B** - t-SNE는 차원 축소 기법으로 상관관계 시각화에 부적절하며, 매 반복 적용 시 훈련 시간 증가
- **옵션 C** - 히스토그램은 데이터 분포만 보여주며 변수 간 상관관계는 시각화할 수 없음, 평균 기준 조정은 최적화 관점에서 비효율적
- **옵션 D** - RMSE는 회귀 모델 평가 지표로 분류 문제에 부적절, AUC가 분류 모델 성능 측정에 적합