---

title: fraud-detection-classification-metrics
created: 2025-08-25
modified: 2025-08-25
tags:
- problem/classification
- usecase/fraud-detection
- constraint/imbalanced-data
- metric/precision-recall
- evaluation/performance
aliases: ["classification-metrics", "fraud-metrics", "imbalanced-metrics"]

---

# 불균형 데이터에서 사기 탐지 분류 지표 선택

## 🎯 핵심 포인트

신용카드 사기 탐지와 같은 불균형 데이터셋(2% 양성)에서 양성 클래스 탐지를 최대화하려는 경우, Precision-Recall AUC와 True Positive Rate(Recall)를 사용하여 모델 성능을 최적화할 수 있다.

## 📝 설명

### 불균형 데이터에서 지표 선택이 중요한 이유

불균형 데이터셋에서는 전통적인 정확도(Accuracy)나 ROC-AUC가 misleading할 수 있습니다. 특히 사기 탐지 시나리오에서는 실제 사기 거래를 놓치는 것(False Negative)이 정상 거래를 사기로 분류하는 것(False Positive)보다 더 큰 비용을 발생시킵니다.

### Confusion Matrix 기반 지표 체계

```
                예측값
              P    N
실제값  P   TP   FN  ← 실제 사기 거래
       N   FP   TN  ← 실제 정상 거래
           ↑    ↑
        사기로  정상으로
        예측    예측
```

### Trade-offs 고려사항

**PR-AUC (Precision-Recall AUC) 장점**:
- 불균형 데이터에서 성능을 종합적으로 평가
- Precision과 Recall의 trade-off를 시각적으로 표현
- 임계값 변화에 따른 성능 변화를 파악 가능

**True Positive Rate (Recall) 장점**:
- 실제 사기 거래 탐지율을 직접 측정
- 비즈니스 목표와 직접적으로 연결
- 해석이 직관적이고 명확

**Accuracy 단점**:
- 불균형 데이터에서 높은 값을 보이지만 의미 없음
- 모든 거래를 정상으로 분류해도 98% 정확도 달성

**ROC-AUC 단점**:
- 불균형 데이터에서 과도하게 낙관적인 결과
- False Positive Rate 기반으로 양성 클래스 성능 왜곡

## 🔍 주요개념

### 핵심 지표 비교

- **Precision (정밀도)**: TP/(TP+FP) - 사기로 예측한 것 중 실제 사기인 비율
- **Recall (재현율) = TPR = Sensitivity**: TP/(TP+FN) - 실제 사기 중 올바르게 탐지한 비율
- **Specificity (특이도) = TNR**: TN/(TN+FP) - 실제 정상 중 올바르게 분류한 비율
- **F1-Score**: 2×(Precision×Recall)/(Precision+Recall) - 정밀도와 재현율의 조화평균

### 용어 동의어 정리

- **Recall = TPR = Sensitivity = 재현율 = 민감도**
- **Specificity = TNR = 특이도**
- **Precision = PPV (Positive Predictive Value) = 정밀도**

### 실전 적용

- **균형 데이터**: Accuracy, F1-Score, ROC-AUC 사용
- **불균형 데이터**: Precision, Recall, PR-AUC 사용
- **양성 탐지 중요**: Recall (TPR) 중심 최적화
- **오탐 최소화 중요**: Precision, Specificity 중심 최적화

## 📝 관련 문제

**Question:** A financial institution is attempting to identify credit card fraud. Around 2% of credit card transactions are fraudulent. The company's objective is to catch as many fraud cases as possible correctly. Which metrics should be used to optimize the model? (Select two.)

**Options:**

- A) Specificity
- B) False positive rate  
- C) Accuracy
- D) Area under the precision-recall curve
- E) True positive rate

**정답: D) Area under the precision-recall curve, E) True positive rate**

💡 추가 설명:

- **A) Specificity** - 정상 거래 식별에 초점을 맞춘 지표로, 사기 탐지 목표와 부합하지 않음
- **B) False positive rate** - 오탐률은 낮을수록 좋지만 사기 탐지 성능을 직접 측정하지 않음  
- **C) Accuracy** - 불균형 데이터에서 misleading하며, 모든 거래를 정상으로 분류해도 98% 달성 가능