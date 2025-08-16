---

title: regression-residual-plots-analysis
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/regression
- technique/residual-analysis
- visualization/plots
- model/evaluation
- ml/diagnostics
aliases: ["residual-plots", "잔차분석", "regression-diagnostics"]

---

# 회귀 모델 잔차 분석 - Residual Plots를 통한 모델 진단

## 🎯 핵심 포인트

회귀 모델에서 예측 편향(over/underestimating)을 탐지하고 모델 성능을 진단하는 경우, Residual plots를 사용하여 체계적인 오차 패턴과 모델의 한계를 시각적으로 분석할 수 있다.

## 📝 설명

### Residual Plots가 회귀 모델 진단에 적합한 이유

잔차(Residual)는 실제값과 예측값의 차이(실제값 - 예측값)로, 모델이 예측하지 못한 부분을 나타냅니다. 집값 예측과 같은 회귀 문제에서 Residual plots는 다음과 같은 핵심 정보를 제공합니다:

- **양수 잔차**: 모델이 과소추정(underestimating) - 실제값 > 예측값
- **음수 잔차**: 모델이 과대추정(overestimating) - 실제값 < 예측값
- **잔차의 크기**: 오차의 정도를 수치적으로 표현

### 수학적 메커니즘

```
잔차 계산: r_i = y_i - ŷ_i
- y_i: 실제값 (actual value)
- ŷ_i: 예측값 (predicted value)
- r_i: 잔차 (residual)

예시:
실제 집값: $500,000, 예측값: $480,000
→ 잔차 = +$20,000 (과소추정)

실제 집값: $300,000, 예측값: $320,000  
→ 잔차 = -$20,000 (과대추정)
```

### 주요 Residual Plot 유형과 활용

1. **Residual vs Predicted Plot**: 예측값에 따른 잔차 분포
2. **Residual Histogram**: 잔차의 전체적인 분포 확인
3. **위치별 Residual Boxplot**: 지역별 예측 편향 비교
4. **Residual vs Features Plot**: 특정 변수와 잔차의 관계

### Trade-offs 고려사항

**Residual Plots 장점**:
- 수치적 편향의 방향과 크기를 동시에 파악
- 위치별, 구간별 상세한 성능 분석 가능
- 모델의 가정(선형성, 등분산성) 검증
- 이상치와 패턴 탐지에 효과적

**Residual Plots 단점**:
- 해석에 통계적 지식 필요
- 복잡한 패턴의 경우 추가 분석 도구 필요

**Confusion Matrix 장점**:
- 직관적이고 이해하기 쉬움
- 분류 성능 요약에 효과적

**Confusion Matrix 단점**:
- 회귀 문제에 직접 적용 불가
- 연속값을 범주로 변환 시 정보 손실
- 수치적 오차의 크기 정보 제공 안됨

## 🔍 주요개념

### 잔차 분석을 통한 모델 진단

- **무작위 분포**: 잔차가 0 중심으로 랜덤하게 분포 → 좋은 모델
- **체계적 패턴**: 특정 구간에서 일관된 편향 → 모델 개선 필요
- **이분산성**: 예측값에 따라 잔차 분산 변화 → 변수 변환 고려
- **비선형 패턴**: 곡선 형태의 잔차 패턴 → 비선형 모델 필요

### 실전 적용 사례

- **부동산 가격 예측**: 지역별 과소/과대추정 패턴 분석으로 지역 특성 변수 추가
- **자동차 연비 예측**: 고배기량 구간에서 과대추정 발견, 비선형 모델 적용
- **주식 가격 예측**: 시계열 잔차에서 자기상관 발견, ARIMA 모델 고려
- **소비자 지출 예측**: 고소득층에서 높은 잔차 분산, 로그 변환 적용
- **의료비 예측**: 중증 환자군에서 과소추정, 별도 모델링 검토

## 📝 관련 문제

**Question:** A Machine Learning Specialist is training a regression model to predict house prices in different locations. The Specialist wants to test the quality of the test data by identifying whether the model is underestimating or overestimating the target price. Which visualization technique should the Specialist use?

**Options:**

- A) Correlation matrix
- B) Confusion matrix  
- C) Root Mean Square Error (RMSE)
- D) Residual plots

**정답: D) Residual plots**

💡 추가 설명:

- **Correlation matrix** - 변수 간 상관관계만 보여주며, 예측 편향의 방향성을 탐지할 수 없음
- **Confusion matrix** - 분류 문제용 도구로 회귀 문제에는 부적합하며, 연속값을 범주화할 경우 수치 정보 손실
- **Root Mean Square Error (RMSE)** - 전체 오차의 크기만 측정하고 over/underestimation 방향성 정보 제공 안됨
- **Residual plots** - 실제값과 예측값의 차이를 시각화하여 체계적인 편향 패턴과 위치별 성능 차이를 명확히 분석 가능