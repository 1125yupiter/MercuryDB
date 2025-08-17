---

title: classification-xgboost-imbalanced
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/classification
- service/sagemaker
- algorithm/xgboost
- constraint/imbalanced-data
- usecase/fraud-detection
aliases: ["fraud-detection", "class-imbalance", "xgb-fraud"]

---

# XGBoost를 활용한 불균형 사기 거래 탐지 시스템

## 🎯 핵심 포인트

극도로 불균형한 사기 거래 데이터셋(1:100 비율)에서 높은 False Negative Rate(87.7%)가 발생한 경우, scale_pos_weight 하이퍼파라미터 조정과 AUC 평가 지표 사용으로 사기 거래 탐지 성능을 향상시킬 수 있다.

## 📝 설명

### XGBoost가 불균형 데이터 사기 탐지에 적합한 이유

XGBoost는 gradient boosting 기반의 앙상블 학습으로 복잡한 패턴을 학습할 수 있으며, scale_pos_weight 파라미터를 통해 클래스 불균형 문제를 효과적으로 해결할 수 있다. 특히 사기 거래 탐지에서는 False Negative(실제 사기를 놓치는 경우)가 직접적인 금전적 손실로 이어지므로, 소수 클래스에 대한 가중치 조정이 필수적이다.

### 아키텍처 플로우

```
Raw Transaction Data (1:100 ratio)
        ↓
Data Preprocessing & Feature Engineering
        ↓
XGBoost Training with scale_pos_weight=100
        ↓
Model Evaluation with AUC metric
        ↓
Threshold Optimization (< 0.5)
        ↓
Production Deployment with Real-time Scoring
```

### Trade-offs 고려사항

**XGBoost + scale_pos_weight 장점**:
- 클래스 불균형 문제를 내재적으로 해결
- 높은 예측 성능과 해석 가능성
- 하이퍼파라미터 튜닝으로 비즈니스 요구사항 반영 가능
- 실시간 추론에 적합한 속도

**XGBoost + scale_pos_weight 단점**:
- False Positive 증가로 인한 고객 불편 가능성
- 하이퍼파라미터 튜닝의 복잡성
- 데이터 분포 변화에 민감

**CNN 기반 모델 장점**:
- 순차적 거래 패턴 학습 가능
- 복잡한 피처 엔지니어링 불필요

**CNN 기반 모델 단점**:
- 불균형 데이터 처리가 복잡
- 훈련 시간과 리소스 요구량 높음
- 해석 가능성 부족

## 🔍 주요개념

### 평가 지표 비교

- **Accuracy**: 전체 예측의 정확도, 불균형 데이터에서 오해의 소지
- **AUC (Area Under Curve)**: ROC 곡선 하단 면적, 임계값 변화에 대한 종합 성능
- **Precision**: 사기로 예측한 것 중 실제 사기 비율
- **Recall**: 실제 사기 중 올바르게 탐지한 비율

### 실전 적용

- **금융 사기 탐지**: 신용카드 거래, 온라인 결제 이상 탐지
- **보험 청구 심사**: 허위 청구 자동 스크리닝
- **이커머스 리스크 관리**: 계정 도용, 결제 사기 방지

## 📝 관련 문제

**Question:** A Machine Learning Specialist is training an XGBoost CNN-based model for detecting fraudulent transactions using Amazon SageMaker AI. The training data contains 5,000 fraudulent behaviors and 500,000 non-fraudulent behaviors. The model reaches an accuracy of 99.5% during training. When tested on the validation dataset, the model shows an accuracy of 99.1% but delivers a high false-negative rate of 87.7%. The Specialist needs to bring down the number of false-negative predictions for the model to be acceptable in production. Which combination of actions must be taken to meet the requirement? (Select TWO.)

**Options:**

- A) Increase the model complexity by specifying a larger value for the max_depth hyperparameter
- B) Alter the value of the eval_metric hyperparameter to MAP (Mean Average Precision)
- C) Adjust the balance of positive and negative weights by configuring the scale_pos_weight hyperparameter
- D) Alter the value of the eval_metric hyperparameter to Area Under The Curve (AUC)
- E) Increase the value of the rate_drop hyperparameter to reduce the overfitting of the model

**정답: C, D) scale_pos_weight 조정 + AUC 평가 지표 사용**

💡 추가 설명:

- **Option A** - 이미 충분한 일반화 성능(99.1% validation accuracy)을 보이므로 복잡도 증가 불필요
- **Option B** - MAP은 랭킹 알고리즘 평가용으로 이진 분류에 부적합
- **Option E** - 오버피팅 문제가 아니므로 정규화 강화는 비효과적

### 핵심 해결 방법

1. **scale_pos_weight 계산**: negative_samples / positive_samples = 500,000 / 5,000 = 100
2. **AUC 기반 평가**: 불균형 데이터에서 accuracy보다 신뢰할 수 있는 지표
3. **임계값 최적화**: 기본 0.5 대신 비즈니스 요구사항에 맞는 임계값 설정
4. **추가 기법**: SMOTE, 언더샘플링, cost-sensitive learning 고려