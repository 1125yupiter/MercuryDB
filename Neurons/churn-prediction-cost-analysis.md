---

title: churn-prediction-cost-analysis
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/churn-prediction
- analysis/cost-benefit
- ml/confusion-matrix
- business/retention
- metric/roi
aliases: ["churn-cost", "retention-roi", "model-feasibility"]

---

# Customer Churn Prediction의 비용-효과 분석

## 🎯 핵심 포인트

고객 이탈 예측 모델을 평가할 때 정확도뿐만 아니라 실제 비즈니스 비용을 계산한 경우, 89% 정확도의 모델이더라도 비용 절감 효과가 있다면 배포할 수 있다.

## 📝 설명

### Customer Churn Prediction이 비즈니스 의사결정에 적합한 이유

고객 이탈 예측은 단순한 분류 문제가 아닌 **비용 최적화 문제**입니다. 각 예측 결과에 따른 실제 비용이 다르기 때문에, 정확도보다는 **총 비용 절감 효과**가 모델 배포 여부를 결정하는 핵심 지표가 됩니다.

### 비즈니스 시나리오 분석

```
비즈니스 상황:
- 고객 이탈 시 손실: $50
- 리텐션 인센티브 비용: $3
- 목표: 총 비용 최소화

모델 예측 결과:
TP: 8명 (이탈 예측 맞음)
FN: 3명 (이탈 놓침)  
FP: 8명 (이탈 잘못 예측)
TN: 81명 (잔류 예측 맞음)
```

### Trade-offs 고려사항

**모델 배포 시 장점**:
- FN(False Negative) 비용이 가장 크므로 이를 줄이는 것이 중요
- TP 케이스에서 $3 인센티브로 $50 손실 방지 가능
- 전체적으로 $352 비용 절약 효과

**모델 배포 시 단점**:
- FP에서 불필요한 인센티브 지급 ($3 × 8명 = $24 낭비)
- 여전히 FN으로 인한 손실 발생 ($50 × 3명 = $150)

**모델 미사용 시 장점**:
- 인센티브 관리 비용 없음
- 단순한 운영 프로세스

**모델 미사용 시 단점**:
- 모든 이탈 고객으로 인한 손실 ($50 × 11명 = $550)
- 예방 가능한 이탈에 대한 대응 불가

## 🔍 주요개념

### 비용 계산 방식 비교

- **정확도 기반**: (TP + TN) / Total = (8 + 81) / 100 = 89%
- **비용 기반**: 실제 발생하는 총 비용으로 모델 가치 평가

### 실전 적용

- **통신사**: 월 요금제 해지 방지를 위한 할인 혜택 제공
- **SaaS 서비스**: 구독 취소 예정 고객에게 추가 기능 무료 제공  
- **금융 서비스**: 계좌 해지 예정 고객에게 우대 금리 제안

## 📊 비용 계산 상세

### 모델 없이 운영하는 경우
```
실제 이탈 고객: TP + FN = 8 + 3 = 11명
총 손실: 11 × $50 = $550
```

### 모델 사용하여 인센티브 제공하는 경우
```
TP (8명): 이탈 예측 맞음 → 인센티브 지급 = 8 × $3 = $24
FN (3명): 이탈 놓침 → 실제 이탈 발생 = 3 × $50 = $150  
FP (8명): 이탈 잘못 예측 → 불필요 인센티브 = 8 × $3 = $24
TN (81명): 잔류 예측 맞음 → 비용 없음 = $0

총 비용: $24 + $150 + $24 + $0 = $198
비용 절감: $550 - $198 = $352
```

## 📝 관련 문제

**Question:** A Machine Learning Specialist at a mobile network company is tasked to develop a model that predicts customers who are likely to cancel subscriptions. Since the customer churn costs about $50, the company plans on giving a $3 retention incentive for unhappy customers. The model was evaluated on a test dataset of 100 customers with TP=8, FN=3, FP=8, TN=81. Is it feasible to deploy the model in production?

**Options:**

- A) No. The model's accuracy is 89%. The total loss is more when the model is deployed than when it is not.
- B) Yes. The model's accuracy is 89%. The total loss is less when the model is deployed than when it is not.
- C) Yes. The model has a precision of 71%, which lags the accuracy.
- D) No. The model has a precision of 95%, which far outweighs the accuracy.

**정답: B) Yes. The model's accuracy is 89%. The total loss is less when the model is deployed than when it is not.**

💡 추가 설명:

- **A 옵션** - 비용 계산 오류: 모델 사용 시 $198 < 미사용 시 $550이므로 손실이 더 적음
- **C 옵션** - 정밀도 계산 오류: Precision = TP/(TP+FP) = 8/16 = 50%, 71%가 아님
- **D 옵션** - 정밀도 계산 오류: 실제 정밀도는 50%이며, 95%가 아님