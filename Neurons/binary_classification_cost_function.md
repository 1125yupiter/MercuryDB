---

title: binary-classification-cost-function-analysis
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/classification
- method/cost-sensitive
- evaluation/confusion-matrix
- business/cost-optimization
- metrics/fn-fp-ratio
aliases: ["cost-function", "cost-sensitive-ml", "binary-cost"]

---

# Binary Classification Cost Function Analysis

## 🎯 핵심 포인트

False Negative과 False Positive의 비즈니스 비용이 다른 경우, 단순 정확도 대신 비용 함수를 최적화하여 실제 비즈니스 가치를 반영한 모델을 선택할 수 있다.

## 📝 설명

### 비용 기반 모델 평가가 비즈니스 의사결정에 적합한 이유

전통적인 머신러닝 평가 지표(정확도, 정밀도, 재현율)는 모든 오류를 동등하게 취급하지만, 실제 비즈니스 환경에서는 서로 다른 유형의 오류가 다른 비용을 발생시킵니다. 비용 함수는 이러한 비즈니스 현실을 모델 선택 과정에 직접 반영하여 실제 가치를 최대화하는 의사결정을 가능하게 합니다.

### 비용 함수 아키텍처 플로우

```
비즈니스 요구사항 분석
         ↓
FN/FP 비용 비율 정의 (예: FN = 3×FP)
         ↓
Cost Function = FP + FN × 비용배수
         ↓
모델별 Confusion Matrix 생성
         ↓
제약조건 필터링 (Recall ≥ 85%, FN Rate ≤ 15%)
         ↓
비용 계산 및 최적 모델 선택
```

### Trade-offs 고려사항

**비용 기반 평가 장점**:
- 실제 비즈니스 영향을 직접 반영
- ROI 기반 의사결정 가능
- 도메인별 특성 고려
- 명확한 모델 선택 기준 제공

**비용 기반 평가 단점**:
- 정확한 비용 추정의 어려움
- 비용 구조 변화 시 재평가 필요
- 복잡한 설정 과정

**단순 정확도 기반 평가 장점**:
- 계산이 간단하고 직관적
- 표준화된 비교 가능

**단순 정확도 기반 평가 단점**:
- 비즈니스 가치 반영 불가
- 클래스 불균형 상황에서 부정확
- 실제 운영 성과와 괴리 가능

## 🔍 주요개념

### 핵심 개념 비교

- **False Negative (FN)**: 실제 양성을 음성으로 잘못 예측 (Type II 오류)
- **False Positive (FP)**: 실제 음성을 양성으로 잘못 예측 (Type I 오류)
- **비용 함수**: Cost = Cost_FN × FN + Cost_FP × FP
- **비용 비율**: 비즈니스 영향도에 따른 FN과 FP의 상대적 비용

### 실전 적용

- **의료 진단**: FN(암 미탐지) >> FP(오진) → FN 최소화 우선
- **스팸 필터**: FP(정상 메일 차단) >> FN(스팸 미탐지) → FP 최소화 우선
- **신용카드 사기 탐지**: FN(사기 미탐지) > FP(정상 거래 차단) → 균형점 찾기

## 📝 관련 문제

**Question:** A Machine Learning Specialist is training a binary classification model for a particular business use case. The company requires: (1) Choose the most cost-effective model given that false negatives are 3 times more expensive than false positives, (2) Choose the model with a recall rate of 85% or more, (3) Choose the model with a false negative rate of 15% or less. Which confusion matrix meets these requirements?

**Options:**

- A) TN = 95, FP = 5, FN = 18, TP = 82
- B) TN = 99, FP = 1, FN = 20, TP = 80  
- C) TN = 82, FP = 18, FN = 15, TP = 85
- D) TN = 75, FP = 25, FN = 11, TP = 89
- E) All models are equally suitable

**정답: D) TN = 75, FP = 25, FN = 11, TP = 89**

💡 추가 설명:

- **Option A** - Recall 82% < 85%, FN Rate 18% > 15% (조건 불만족)
- **Option B** - Recall 80% < 85%, FN Rate 20% > 15% (조건 불만족)  
- **Option C** - Cost = 18 + 15×3 = 63 (조건 만족하지만 비용 높음)
- **Option D** - Cost = 25 + 11×3 = 58 (모든 조건 만족, 최저 비용)

---