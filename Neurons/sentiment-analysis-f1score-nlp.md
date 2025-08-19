---

title: sentiment-analysis-f1score-nlp
created: 2025-08-19
modified: 2025-08-19
tags:
- evaluation/f1score
- nlp/sentiment-analysis
- metrics/classification
- imbalanced/dataset
- performance/measurement
aliases: ["f1-score", "sentiment-eval", "nlp-metrics"]

---

# F1-Score를 활용한 감정 분석 모델 평가

## 🎯 핵심 포인트

클래스 불균형이 존재하는 감정 분석 작업에서, F1-score를 사용할 경우 정밀도와 재현율의 조화 평균으로 모델의 전반적인 성능을 균형있게 평가할 수 있다.

## 📝 설명

### F1-score가 감정 분석에 적합한 이유

F1-score는 정밀도(Precision)와 재현율(Recall)의 조화 평균으로, 감정 분석과 같은 NLP 작업에서 가장 효과적인 평가 지표입니다. 감정 분석 데이터셋은 종종 클래스 불균형(예: 중립 리뷰가 긍정/부정 리뷰보다 많음)을 보이는데, F1-score는 이러한 상황에서 단순 정확도보다 신뢰할 수 있는 성능 측정을 제공합니다.

### 아키텍처 플로우

```
감정 분석 데이터셋
    ↓
클래스 분포 확인 (불균형 여부)
    ↓
모델 훈련 및 예측
    ↓
성능 평가 지표 계산
    ↓
F1-score = 2 × (Precision × Recall) / (Precision + Recall)
    ↓
모델 성능 종합 판단
```

### Trade-offs 고려사항

**F1-score 장점**:
- 정밀도와 재현율을 모두 고려한 균형잡힌 평가
- 클래스 불균형 상황에서 신뢰할 수 있는 성능 측정
- 단일 지표로 모델 성능을 간결하게 표현
- 다중 클래스 분류에서 macro/weighted 평균 적용 가능

**F1-score 단점**:
- 특정 클래스에 대한 세부적인 성능 분석이 어려움
- 비즈니스 요구사항에 따른 정밀도/재현율 중요도 차이 반영 불가
- 임계값 변화에 따른 성능 변화 추이 파악 어려움

**Accuracy 장점**:
- 직관적이고 이해하기 쉬운 지표
- 전체 예측의 정확성을 간단히 표현

**Accuracy 단점**:
- 클래스 불균형 상황에서 오해를 불러일으킬 수 있음
- 다수 클래스 편향으로 인한 성능 과대평가 위험

## 🔍 주요개념

### 핵심 평가 지표 비교

- **Precision (정밀도)**: 모델이 양성으로 예측한 것 중 실제 양성인 비율 (TP / (TP + FP))
- **Recall (재현율)**: 실제 양성 중 모델이 양성으로 올바르게 예측한 비율 (TP / (TP + FN))
- **F1-score**: 정밀도와 재현율의 조화 평균, 두 지표의 균형을 고려
- **Accuracy**: 전체 예측 중 올바른 예측의 비율, 클래스 불균형 시 오해 소지

### 실전 적용

- **고객 리뷰 감정 분석**: 중립 리뷰가 대부분인 상황에서 긍정/부정 감정을 정확히 식별
- **소셜 미디어 모니터링**: 브랜드 관련 언급의 감정을 균형있게 평가하여 마케팅 전략 수립
- **제품 피드백 분석**: 불만족 리뷰를 놓치지 않으면서도 거짓 경보를 최소화하는 모델 구축

## 📝 관련 문제

**Question:** A company is developing a sentiment analysis model to classify customer reviews as positive, negative, or neutral. The dataset contains 60% neutral reviews, 25% positive reviews, and 15% negative reviews. Which evaluation metric would be most appropriate for measuring the model's performance in this imbalanced classification scenario?

**Options:**

- A) Accuracy
- B) Precision
- C) Recall  
- D) F1-score
- E) None of the above

**정답: D) F1-score**

💡 추가 설명:

- **A) Accuracy** - 클래스 불균형 상황에서 다수 클래스(중립)에 편향된 결과를 보여줄 수 있어 부적합
- **B) Precision** - 거짓 양성은 줄이지만 실제 감정을 놓치는 경우(거짓 음성)를 고려하지 못함
- **C) Recall** - 실제 감정을 잘 찾아내지만 거짓 양성 문제를 간과할 수 있음
- **E) None of the above** - F1-score가 적절한 선택지로 존재하므로 부적합