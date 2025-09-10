---
title: imbalanced-classification-metrics-evaluation
created: 2025-08-28
modified: 2025-08-28
tags:
- problem/classification
- constraint/imbalanced-data
- evaluation/metrics
- technique/model-selection
- domain/medical-screening
aliases: ["imbalanced-metrics", "pr-auc", "model-evaluation"]

---

# 불균형 분류 문제에서의 성능 지표 선택

## 🎯 핵심 포인트

극도로 불균형한 데이터셋(양성 클래스 < 10%)에서 분류 모델을 평가할 경우, PR AUC를 사용하여 실제 양성 클래스 탐지 성능을 정확하게 측정할 수 있다.

## 📝 설명

### 불균형 데이터셋에서의 지표 선택이 중요한 이유

암 스크리닝, 사기 거래 탐지와 같은 실제 업무에서는 양성 클래스(관심 클래스)의 비율이 매우 낮습니다. 이런 상황에서 잘못된 평가 지표를 선택하면 실제로는 성능이 나쁜 모델을 좋은 모델로 착각할 수 있습니다. 특히 의료 진단처럼 False Negative(실제 양성을 놓치는 것)의 비용이 매우 클 때는 더욱 신중한 지표 선택이 필요합니다.

### 평가 지표별 특성 비교

```
불균형 데이터 (암환자 1%, 건강인 99%)
         ↓
    모델 예측 결과
         ↓
┌─────────────────────────┐
│ TP=80, FP=500          │
│ FN=20, TN=9400         │  
└─────────────────────────┘
         ↓
┌─────────────────────────┐
│ ROC AUC: 높게 계산됨    │
│ (TN이 압도적으로 많아서) │
└─────────────────────────┘
         ↓
┌─────────────────────────┐
│ PR AUC: 낮게 계산됨     │
│ (실제 정확도 반영)      │
└─────────────────────────┘
```

### Trade-offs 고려사항

**ROC AUC 장점**:
- 전체적인 분류 성능을 종합적으로 평가
- 균형잡힌 데이터에서 안정적인 성능 측정
- 다양한 임계값에서의 성능 변화 파악 가능

**ROC AUC 단점**:
- 극도로 불균형한 데이터에서 과대평가 위험
- True Negative가 압도적으로 많을 때 FPR이 인위적으로 낮아짐
- 실제 운영 환경에서의 성능과 괴리 발생 가능

**PR AUC 장점**:
- 불균형 데이터에서 실제 성능을 정확하게 반영
- 양성 예측의 정확성(Precision)과 완전성(Recall)을 균형있게 평가
- True Negative 수에 영향받지 않음

**PR AUC 단점**:
- 균형잡힌 데이터에서는 ROC AUC보다 해석이 복잡
- 음성 클래스의 성능은 직접 평가하지 않음

## 🔍 주요개념

### 지표별 사용 시점

- **ROC AUC**: 양성:음성 비율이 20:80 이상, 두 클래스 모두 중요한 경우
- **PR AUC**: 양성:음성 비율이 10:90 미만, 양성 클래스 탐지가 핵심인 경우
- **Precision**: 양성 예측의 정확성이 중요한 경우 (단독 사용은 부적합)
- **Recall**: 실제 양성을 놓치면 안 되는 경우 (단독 사용은 부적합)

### 실전 적용

- **의료 진단**: 암 스크리닝, 희귀질환 진단 → PR AUC 사용
- **보안 시스템**: 사기 거래 탐지, 침입 탐지 → PR AUC 사용  
- **마케팅**: 고객 이탈 예측, 구매 예측 → 데이터 균형도에 따라 선택
- **품질 관리**: 불량품 탐지, 이상 탐지 → PR AUC 사용
- **스팸 필터링**: 정상 메일 보호도 중요 → ROC AUC 고려

## 📝 관련 문제

**Question:** You are a data scientist working for a cancer screening center. The center has gathered data on many patients that have been screened over the years. The data is obviously skewed toward true negative results, as most screened patients don't have cancer. You evaluate several machine learning models to decide which model best predicts true positives when using your cancer screening data. You have split your data into a 70/30 ratio of the training set to the test set. You now need to decide which metric to use to evaluate your models. Which metric will most accurately determine the model best suited to solve your classification problem?

**Options:**

- A) ROC Curve
- B) Precision  
- C) Recall
- D) PR AUC
- E) F1 Score

**정답: D) PR AUC**

💡 추가 설명:

- **ROC AUC (A)** - 극도로 불균형한 데이터에서 True Negative가 압도적으로 많아 FPR이 인위적으로 낮아져 성능을 과대평가
- **Precision (B)** - 양성 예측의 정확성만 측정하므로 단독으로는 불충분한 평가 지표
- **Recall (C)** - 실제 양성 중 탐지율만 측정하므로 단독으로는 불충분한 평가 지표  
- **F1 Score (E)** - Precision과 Recall의 조화평균이지만 임계값 고정으로 전체 성능 곡선을 보지 못함