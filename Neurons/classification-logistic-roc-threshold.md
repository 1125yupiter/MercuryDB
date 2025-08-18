---

title: classification-logistic-roc-threshold
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/classification
- algorithm/logistic-regression
- evaluation/roc-curve
- optimization/threshold
- usecase/hotel-booking
aliases: ["ROC", "AUC", "threshold-optimization"]

---

# 로지스틱 회귀 분류 임계값 최적화를 위한 ROC 커브 활용

## 🎯 핵심 포인트

이진 분류 모델에서 최적의 분류 임계값을 찾고자 하는 경우 ROC 커브에서, 다양한 임계값에서의 성능을 종합적으로 평가하고 AUC를 통해 모델 품질을 측정할 수 있다.

## 📝 설명

### ROC 커브가 분류 임계값 최적화에 적합한 이유

ROC(Receiver Operating Characteristic) 커브는 이진 분류 모델의 임계값 변화에 따른 성능을 시각화하는 도구입니다. 임계값을 0에서 1까지 변화시키면서 각 지점에서의 True Positive Rate(민감도)와 False Positive Rate(1-특이도)를 플롯하여 모델의 판별 능력을 종합적으로 평가할 수 있습니다.

ROC 커브의 핵심 장점은 임계값에 독립적인 평가가 가능하다는 점입니다. AUC(Area Under Curve) 값을 통해 모델의 전반적인 성능을 단일 지표로 측정하며, 0.5는 무작위 분류기, 1.0은 완벽한 분류기를 의미합니다. 호텔 예약 취소 예측과 같은 비즈니스 문제에서는 취소 고객을 놓치는 위험과 정상 고객을 취소로 오분류하는 위험 간의 균형점을 찾을 수 있습니다.

### 아키텍처 플로우

```
로지스틱 회귀 모델 훈련
        ↓
임계값 범위 설정 (0 ~ 1)
        ↓
각 임계값에서 예측 수행
        ↓
TPR, FPR 계산 및 ROC 포인트 생성
        ↓
ROC 커브 플롯 및 AUC 계산
        ↓
최적 임계값 선택 (비즈니스 요구사항 반영)
        ↓
모델 배포 및 성능 모니터링
```

### Trade-offs 고려사항

**ROC 커브 장점**:
- 임계값 독립적인 종합적 성능 평가
- AUC를 통한 모델 간 객관적 비교 가능
- 시각적으로 직관적인 성능 해석
- 비즈니스 요구사항에 맞는 임계값 선택 지원

**ROC 커브 단점**:
- 클래스 불균형이 심한 경우 과도하게 낙관적 평가 가능
- Precision-Recall 커브만큼 세밀한 양성 클래스 중심 분석 어려움

**RMSE 장점**:
- 연속값 예측에서 직관적인 오차 측정
- 이상치에 민감하여 큰 오차를 강하게 패널티

**RMSE 단점**:
- 분류 문제에 부적합 (확률값이 아닌 연속값 평가용)
- 임계값 최적화와 무관한 지표

## 🔍 주요개념

### 성능 지표 비교

- **ROC-AUC**: 임계값 독립적 분류 성능 평가, 전체적인 판별 능력 측정
- **Precision-Recall AUC**: 양성 클래스 중심 평가, 클래스 불균형 상황에 적합
- **F1-Score**: 특정 임계값에서의 정밀도와 재현율의 조화평균
- **RMSE**: 회귀 문제용 오차 측정 지표

### 실전 적용

- **호텔 예약 취소 예측**: 취소 위험 고객 식별을 통한 선제적 고객 관리
- **의료진단**: 질병 발병 확률 예측에서 민감도-특이도 균형 최적화
- **금융 신용평가**: 대출 부실 위험 평가에서 승인율과 손실률 균형

## 📝 관련 문제

**Question:** A Machine Learning Specialist is training a logistic regression model for predicting hotel booking cancellations. The Specialist hopes to develop the best version of the model with the optimal classification threshold score. For this reason, he needs to evaluate how various threshold settings affect model performance. Which approach should the Specialist apply?

**Options:**

- A) Evaluate the model using the Root Mean Square Error (RMSE)
- B) Evaluate the model using the L1 norm
- C) Evaluate the model using a scatter plot graph  
- D) Evaluate the model using the Receiver Operating Characteristic Curve (ROC)

**정답: D) Receiver Operating Characteristic Curve (ROC)**

💡 추가 설명:

- **A) RMSE** - 회귀 모델에서 연속값 예측 오차를 측정하는 지표로, 분류 문제의 임계값 최적화와는 무관
- **B) L1 norm** - 정규화 기법 중 하나로 모델의 복잡도를 제어하는 방법이며, 성능 평가 지표가 아님
- **C) Scatter plot** - 두 변수 간 관계를 시각화하는 도구로, 분류 임계값 최적화에는 직접적 도움이 되지 않음

---