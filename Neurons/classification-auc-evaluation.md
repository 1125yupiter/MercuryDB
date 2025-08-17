---

title: classification-auc-evaluation
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/classification
- metric/auc
- evaluation/model-comparison
- performance/true-positive
- method/supervised
aliases: ["AUC", "ROC", "model-ranking", "classification-metrics"]

---

# 머신러닝 분류 모델 성능 평가 - AUC를 활용한 True Positive 예측 능력 비교

## 🎯 핵심 포인트

여러 분류 모델의 true positive 예측 능력을 비교하여 랭킹을 매기는 경우, Area Under the ROC Curve (AUC)를 사용하여 임계값에 독립적이고 객관적인 모델 성능 평가를 할 수 있다.

## 📝 설명

### AUC가 분류 모델 비교에 적합한 이유

AUC는 Receiver Operating Characteristic (ROC) 곡선 아래의 면적을 측정하는 지표로, 모델이 양성 샘플에 대해 음성 샘플보다 높은 점수를 예측하는 능력을 평가합니다. 특히 true positive 예측 능력 측정에 최적화되어 있으며, 임계값 설정 없이도 모델의 전반적인 분류 성능을 객관적으로 비교할 수 있습니다.

### 평가 플로우

```
훈련된 분류 모델들
↓
각 모델의 예측 확률 계산
↓
ROC 곡선 생성 (TPR vs FPR)
↓
AUC 값 계산 (0~1 범위)
↓
AUC 기준 모델 랭킹
↓
최종 모델 선택
```

### Trade-offs 고려사항

**AUC 장점**:
- 임계값에 독립적인 평가 가능
- 모델 간 객관적 비교 용이
- True positive 예측 능력 직접 측정
- 표준화된 0~1 범위로 해석 간편
- 불균형 데이터에서도 안정적

**AUC 단점**:
- 클래스 불균형이 극심한 경우 과도하게 낙관적
- 실제 비즈니스 임계값 고려 불가
- Precision 중심 평가 시 한계

**Specificity 장점**:
- True negative rate 직접 측정
- 특정 임계값에서의 성능 명확

**Specificity 단점**:
- 전체 모델 성능 평가에 불충분
- 단일 지표로 모델 랭킹 어려움
- True positive 예측 능력 직접 측정 불가

## 🔍 주요개념

### 핵심 지표 비교

- **AUC (Area Under ROC Curve)**: 모든 임계값에서의 TPR vs FPR 성능을 종합한 단일 지표
- **Specificity**: 특정 임계값에서의 True Negative Rate (TN/(TN+FP))
- **Sensitivity (Recall)**: 특정 임계값에서의 True Positive Rate (TP/(TP+FN))
- **ROC Curve**: 다양한 임계값에서 TPR과 FPR의 관계를 시각화

### 실전 적용

- **신용 평가 모델**: 여러 알고리즘의 채무불이행 예측 성능 비교
- **의료 진단 시스템**: 질병 탐지 모델들의 true positive 식별 능력 평가
- **사기 탐지**: 금융 거래에서 사기 거래 식별 모델 성능 랭킹

## 📝 관련 문제

**Question:** A Machine Learning Specialist will be using different sets of training data to evaluate the various machine learning classification models against each other. She wants to rank each generated model by its ability to predict true positives. Which performance metric is the MOST appropriate for the problem?

**Options:**

- A) Specificity
- B) Mean Absolute Percentage Error (MAPE)  
- C) Root-mean-square error (RMSE)
- D) Area Under the ROC Curve (AUC)

**정답: D) Area Under the ROC Curve (AUC)**

💡 추가 설명:

- **Specificity** - True negative rate만 측정하므로 true positive 예측 능력 평가에는 불충분하며, 전체 모델 성능 비교를 위한 단일 지표로 부적합
- **MAPE** - 회귀 모델의 백분율 오차 측정 지표로 분류 문제에는 적용 불가능
- **RMSE** - 회귀 모델의 평균 제곱근 오차 측정 지표로 분류 모델 평가에는 부적합
- **AUC** - 모든 임계값에서 true positive 예측 능력을 종합 평가하여 모델 간 객관적 비교가 가능한 최적 지표