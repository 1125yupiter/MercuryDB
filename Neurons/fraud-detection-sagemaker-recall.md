---

title: fraud-detection-sagemaker-recall
created: 2025-08-19
modified: 2025-08-19
tags:
- problem/classification
- service/sagemaker
- constraint/cost-sensitive
- technique/recall-optimization
- usecase/insurance-fraud
aliases: ["fraud-detection", "recall-optimization", "cost-sensitive-ml"]

---

# 보험 사기 탐지에서 비대칭적 비용 구조를 고려한 Recall 최적화

## 🎯 핵심 포인트

가짜 클레임을 처리하는 비용이 잠재적 사기를 조사하는 비용보다 훨씬 큰 경우, Amazon SageMaker Model Monitor에서 Recall을 최적화하여 모든 사기 클레임을 놓치지 않을 수 있다.

## 📝 설명

### SageMaker Model Monitor가 사기 탐지에 적합한 이유

보험 사기 탐지는 **비대칭적 비용 구조**를 가지는 대표적인 분류 문제입니다. False Negative(사기를 놓치는 것)의 비용이 False Positive(조사 비용)보다 훨씬 크기 때문에, 모든 잠재적 사기를 식별하는 것이 중요합니다.

SageMaker Model Monitor는 실시간으로 모델 성능을 모니터링하고, Recall 지표가 임계값 이하로 떨어질 때 즉시 알림을 제공하여 사기 탐지 성능을 지속적으로 보장합니다.

### 아키텍처 플로우

```
클레임 데이터 → SageMaker 엔드포인트 → 분류 결과
     ↓
Model Monitor → Recall 계산 → 임계값 비교
     ↓
Recall < 임계값 → CloudWatch 알림 → 모델 재훈련/조정
```

### Trade-offs 고려사항

**Recall 최적화 장점**:
- 모든 사기 클레임을 놓치지 않음 (False Negative 최소화)
- 높은 재정 손실 방지
- 지속적인 모니터링으로 성능 보장

**Recall 최적화 단점**:
- False Positive 증가로 인한 조사 비용 상승
- 정당한 클레임의 지연 처리 가능성
- 고객 만족도에 영향

**Precision 최적화 장점**:
- 조사 비용 절약 (False Positive 감소)
- 정당한 클레임의 빠른 처리

**Precision 최적화 단점**:
- 사기 클레임을 놓칠 위험 (높은 재정 손실)
- 비즈니스 목표와 상충

## 🔍 주요개념

### 평가 지표 비교

- **Recall (민감도)**: TP/(TP+FN) - 실제 사기 중 탐지한 비율
- **Precision (정밀도)**: TP/(TP+FP) - 사기로 분류한 것 중 실제 사기 비율
- **F1 Score**: 2×(Precision×Recall)/(Precision+Recall) - 균형 지표
- **Accuracy**: (TP+TN)/(TP+TN+FP+FN) - 전체 정확도

### 실전 적용

- **보험 회사**: 사기 클레임으로 인한 손실이 조사 비용보다 10배 이상 클 때
- **신용카드 회사**: 도용 거래 탐지에서 고객 보호가 최우선일 때  
- **의료 보험**: 부정 청구로 인한 손실이 심사 비용을 크게 초과할 때

## 📝 관련 문제

**Question:** A fintech company has developed a machine learning model that classifies insurance claims into fraudulent or legitimate. Given the financial implications where the expense of processing a fraudulent claim surpasses the cost associated with investigating a potentially fraudulent claim, which evaluation metric is most appropriate for assessing the performance of this classification model to ensure optimal financial outcomes?

**Options:**

- A) Implement a custom F1 score metric within Amazon SageMaker, balancing the precision and recall, without specifically targeting the cost implications of false negatives versus false positives.
- B) Employ Amazon SageMaker Model Monitor to optimize for recall, prioritizing the identification of all potential fraudulent claims, even at the risk of higher investigation costs due to false positives.
- C) Use Amazon SageMaker's automatic hyperparameter tuning focusing on precision, to decrease the occurrence of false positives in classifying claims as fraudulent.
- D) Apply Amazon SageMaker's built-in evaluation metrics to emphasize model accuracy, aiming for a high percentage of correct predictions overall.

**정답: B) Employ Amazon SageMaker Model Monitor to optimize for recall**

💡 추가 설명:

- **Option A (F1 Score)** - Precision과 Recall의 균형을 맞추지만, 비대칭적 비용 구조를 반영하지 못함
- **Option C (Precision 최적화)** - False Positive는 줄이지만 사기를 놓칠 위험이 높아져 더 큰 손실 발생
- **Option D (Accuracy)** - 전체적인 정확도만 고려하여 클래스 불균형과 비용 차이를 무시함