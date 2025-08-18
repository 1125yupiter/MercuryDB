---

title: confusion-matrix-recall-fraud
created: 2025-08-18 
modified: 2025-08-18 
tags:
- metrics/recall
- usecase/fraud-detection
- evaluation/classification
- finance/transaction
- imbalanced/data
aliases: ["confusion matrix", "recall metric", "fraud detection"]

---

# 금융 사기 탐지에서 컨퓨전 매트릭스와 리콜 평가 지표

## 🎯 핵심 포인트

불균형한 금융 거래 데이터에서 사기 탐지 모델을 평가할 경우, 리콜(Recall) 지표를 통해 실제 사기 거래 중 모델이 올바르게 탐지한 비율을 측정할 수 있다.

## 📝 설명

### 컨퓨전 매트릭스가 금융 사기 탐지에 적합한 이유

컨퓨전 매트릭스는 이진 분류 모델의 성능을 시각적으로 평가할 수 있는 핵심 도구입니다. 특히 금융 사기 탐지와 같이 클래스 불균형이 심한 상황에서 단순한 정확도보다 더 세밀한 평가가 가능합니다. 실제 운영 환경에서는 사기 거래를 놓치는 것(False Negative)이 정상 거래를 사기로 분류하는 것(False Positive)보다 더 큰 비즈니스 손실을 야기할 수 있어, 각 유형의 오류를 별도로 측정해야 합니다.

### 아키텍처 플로우

```
거래 데이터 입력
       ↓
사기 탐지 모델 예측
       ↓
컨퓨전 매트릭스 생성
       ↓
            예측값
         Fraud  Normal
실제값 Fraud  TP    FN
      Normal FP    TN
       ↓
리콜 계산: TP/(TP+FN)
       ↓
모델 성능 평가 및 최적화
```

### Trade-offs 고려사항

**리콜(Recall) 중심 평가 장점**:
- 실제 사기 거래의 탐지 비율을 직접적으로 측정
- 비즈니스 손실과 직결된 False Negative 최소화 가능
- 불균형 데이터셋에서도 의미있는 평가 지표 제공

**리콜(Recall) 중심 평가 단점**:
- False Positive(오탐) 증가 위험으로 고객 불편 야기
- 정밀도와 트레이드오프 관계로 균형점 찾기 어려움
- 단독 사용 시 전체 모델 성능 파악 제한적

**정확도(Accuracy) 중심 평가 장점**:
- 전체적인 모델 성능을 하나의 지표로 간단히 표현
- 직관적이고 이해하기 쉬운 지표

**정확도(Accuracy) 중심 평가 단점**:
- 클래스 불균형 상황에서 misleading한 결과 제공
- 사기 탐지와 같은 중요한 소수 클래스 성능 가려짐
- 비즈니스 영향도를 반영하지 못함

## 🔍 주요개념

### 평가 지표 비교

- **리콜(Recall)**: TP/(TP+FN) - 실제 양성 중 올바르게 예측한 비율 (민감도)
- **정밀도(Precision)**: TP/(TP+FP) - 양성 예측 중 실제 양성인 비율
- **정확도(Accuracy)**: (TP+TN)/전체 - 전체 예측 중 올바른 예측 비율
- **특이도(Specificity)**: TN/(TN+FP) - 실제 음성 중 올바르게 예측한 비율

### 실전 적용

- **실시간 결제 승인**: 높은 리콜로 사기 거래 차단하되, 정밀도도 고려하여 정상 고객 불편 최소화
- **일괄 처리 사기 분석**: 리콜 우선으로 의심 거래 최대 탐지 후 인간 검토자가 2차 필터링
- **위험도 기반 계층 분석**: 고위험 거래는 높은 리콜, 저위험 거래는 정밀도 중심으로 차등 적용

## 📝 관련 문제

**Question:** A financial institution deployed a fraud detection model that resulted in the following confusion matrix for transaction classification. If TP=90, FN=10, FP=27, TN=873, what is the recall of this model?

**Options:**

- A) 66.67%
- B) 87.3%
- C) 90%
- D) 96.3%
- E) 74%

**정답: C) 90%**

💡 추가 설명:

- **A) 66.67%** - 이는 정밀도(Precision = TP/(TP+FP) = 90/117 = 76.9%)와 혼동한 계산
- **B) 87.3%** - 전체 정확도와 유사하지만 잘못된 계산
- **D) 96.3%** - 전체 정확도(Accuracy = (TP+TN)/전체 = 963/1000)와 혼동
- **E) 74%** - F1-score나 다른 복합 지표와 혼동한 결과

리콜은 실제 사기 거래(TP+FN=100) 중에서 모델이 올바르게 탐지한 사기 거래(TP=90)의 비율이므로 90/100 = 90%입니다. 이는 모델이 실제 사기 거래의 90%를 성공적으로 탐지했다는 의미입니다.