---

title: regression-residual-overestimate-analysis
created: 2025-08-24 
modified: 2025-08-24 
tags:
- problem/regression
- method/residual-analysis
- evaluation/model-bias
- technique/error-direction
- metric/prediction-accuracy
aliases: ["residual-plots", "overestimate-underestimate", "regression-bias"]

---

# 회귀 모델의 과대/과소추정 판단을 위한 잔차 분석

## 🎯 핵심 포인트

회귀 모델이 목표값을 과대추정 또는 과소추정하는 경향을 파악해야 하는 경우, 잔차 플롯에서 오차의 방향성을 분석할 수 있다.

## 📝 설명

### 잔차 분석이 모델 편향 탐지에 적합한 이유

잔차(Residual)는 실제값에서 예측값을 뺀 값으로, 모델의 예측 오차를 나타냅니다. 잔차 플롯을 통해 모델이 체계적으로 과대추정하는지 과소추정하는지를 시각적으로 명확하게 파악할 수 있습니다.

**잔차 = 실제값 - 예측값**
- 잔차가 양수: 실제값 > 예측값 → 과소추정(underestimate)
- 잔차가 음수: 실제값 < 예측값 → 과대추정(overestimate)

### 분석 플로우

```
데이터 수집
    ↓
모델 예측 수행
    ↓
잔차 계산 (실제값 - 예측값)
    ↓
잔차 플롯 생성 및 시각화
    ↓
패턴 분석 (양수/음수 분포)
    ↓
편향 방향 판단
```

### Trade-offs 고려사항

**잔차 플롯 장점**:
- 오차의 방향성을 명확하게 시각화
- 개별 데이터 포인트별 편향 패턴 확인 가능
- 모델의 체계적 편향을 직관적으로 파악
- 추가적인 모델 개선 방향 제시

**잔차 플롯 단점**:
- 대용량 데이터에서는 시각적 해석이 복잡할 수 있음
- 정량적 지표가 아닌 정성적 분석 도구

**RMSE 장점**:
- 정량적인 오차 크기 측정
- 모델 성능 비교에 유용

**RMSE 단점**:
- 제곱근 처리로 인해 오차의 방향성 정보 손실
- 과대/과소추정 여부 판단 불가능

## 🔍 주요개념

### 평가 지표 비교

- **잔차 플롯**: 오차의 방향성과 패턴을 시각화하여 모델 편향 탐지
- **RMSE**: 오차의 크기만 측정, 방향성 정보 없음
- **AUC**: 분류 문제용 지표, 회귀 문제에 적용 불가
- **Confusion Matrix**: 분류 문제용 도구, 회귀 문제에 적용 불가

### 실전 적용

- 주택 가격 예측 모델에서 특정 지역의 가격을 지속적으로 과대추정하는지 확인
- 매출 예측 모델이 특정 시즌에 과소추정하는 경향이 있는지 분석
- 의료 진단에서 특정 환자군에 대한 위험도를 과소평가하는지 검토

## 📝 관련 문제

**Question:** A Machine Learning Specialist developed a regression model, but the first iteration requires optimization. The Specialist must determine if the model over- or underestimates the objective more often. Which option does the Specialist have for determining if the goal number is being over- or underestimated?

**Options:**

- A) Root Mean Square Error (RMSE)
- B) Residual plots
- C) Area under the curve
- D) Confusion matrix

**정답: B) Residual plots**

💡 추가 설명:

- **A) RMSE** - 오차의 크기만 알 수 있고 방향성(과대/과소추정)은 판단할 수 없음
- **C) Area under the curve** - 분류 문제에서 사용하는 지표로 회귀 문제의 편향 분석과 무관
- **D) Confusion matrix** - 분류 문제 전용 도구로 회귀 문제에 적용 불가능