---

title: classification-mlp-recall-improvement
created: 2025-08-25
modified: 2025-08-25
tags:
- problem/classification
- method/neural-network
- technique/class-balancing
- constraint/imbalanced-data
- evaluation/recall
aliases: ["mlp-recall", "class-imbalance-mlp", "neural-network-recall"]

---

# MLP 다중클래스 분류에서 재현율 개선 전략

## 🎯 핵심 포인트

다중클래스 분류에서 특정 클래스의 재현율이 낮은 경우 클래스 불균형 문제에서, 클래스 가중치 조정이나 알고리즘 변경을 통해 빠른 성능 개선을 할 수 있다.

## 📝 설명

### MLP에서 재현율 문제가 발생하는 이유

재현율(Recall)이 낮다는 것은 실제 양성 클래스를 음성으로 잘못 분류하는 **False Negative**가 많다는 의미입니다. MLP에서 이런 문제가 발생하는 주요 원인은:

1. **클래스 불균형**: 목표 클래스의 샘플 수가 다른 클래스에 비해 적음
2. **손실 함수의 편향**: 다수 클래스에 최적화된 학습
3. **결정 임계값 문제**: 기본 0.5 임계값이 불균형 데이터에 부적합

### 문제 해결 플로우

```
클래스 불균형 감지
        ↓
클래스 가중치 조정 (Class Weights)
        ↓
손실 함수 수정 (Weighted Loss)
        ↓
모델 재훈련
        ↓
재현율 평가 및 임계값 조정
        ↓
성능 개선 확인
```

### Trade-offs 고려사항

**클래스 가중치 조정 장점**:
- 기존 모델 구조 유지하면서 빠른 개선 가능
- 불균형 데이터에 직접적으로 대응
- 구현이 간단하고 즉시 적용 가능
- 하이퍼파라미터 튜닝으로 세밀한 조정 가능

**클래스 가중치 조정 단점**:
- 정밀도(Precision) 감소 가능성
- 과적합 위험 증가
- 최적 가중치 찾기 위한 실험 필요

**XGBoost 대체 장점**:
- 불균형 데이터에서 일반적으로 우수한 성능
- 내장된 정규화로 과적합 방지
- 빠른 훈련 속도
- 피처 중요도 분석 가능

**XGBoost 대체 단점**:
- 새로운 모델 학습 및 검증 필요
- 하이퍼파라미터 재조정 필요
- 딥러닝 특성 손실

## 🔍 주요개념

### 성능 지표 비교

- **재현율(Recall)**: TP / (TP + FN) - 실제 양성을 얼마나 잘 찾는가
- **정밀도(Precision)**: TP / (TP + FP) - 양성 예측이 얼마나 정확한가
- **F1-Score**: 2 × (Precision × Recall) / (Precision + Recall) - 균형잡힌 지표

### 실전 적용

- **의료 진단**: 질병 감지에서 놓치는 케이스(FN) 최소화가 중요
- **사기 탐지**: 실제 사기를 놓치지 않는 것이 우선순위
- **스팸 필터링**: 중요한 이메일을 스팸으로 분류하지 않도록 주의

## 📝 관련 문제

**Question:** A data scientist is training a multilayer perceptron (MLP) algorithm on a multiclass dataset. Although the target class is distinct from the other classes in the dataset, it does not attain an adequate recall score. The Data Scientist has already experimented with altering the number and size of the MLP's hidden layers, but the results have not improved considerably. A recall solution must be implemented as soon as feasible. Which strategies are appropriate for meeting these requirements?

**Options:**

- A) Gather more data using Amazon Mechanical Turk and then retrain
- B) Train an anomaly detection model instead of an MLP  
- C) Train an XGBoost model instead of an MLP
- D) Add class weights to the MLP's loss function and then retrain

**정답: C, D) XGBoost 모델 훈련 및 클래스 가중치 추가**

💡 추가 설명:

- **Option A** - 데이터 수집과 레이블링에 시간이 오래 걸려 "빠른 해결책" 요구사항에 부적합
- **Option B** - 다중클래스 분류 문제를 이상탐지로 바꾸는 것은 원래 요구사항과 다른 문제 정의로 변경하는 것