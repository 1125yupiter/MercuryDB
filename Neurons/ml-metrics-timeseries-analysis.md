---
title: ml-metrics-timeseries-analysis
created: 2025-08-24
modified: 2025-08-24
tags:
- metrics/mae
- metrics/rmse
- metrics/mape
- visualization/scatter-plot
- analysis/time-series
aliases: ["MAE", "RMSE", "MAPE", "model-metrics", "forecast-evaluation"]

---

# ML 모델 성능 지표와 시계열 분석 방법론

## 🎯 핵심 포인트

모델 성능 저하를 분석할 경우 시계열 그래프에서 MAE, RMSE, MAPE 등의 성능 지표 추이를 확인할 수 있고, 변수 간 관계 분석이 필요한 경우 산점도를 활용할 수 있다.

## 📝 설명

### 예측 모델 성능 지표의 이해와 적용

예측 모델의 성능을 평가하기 위해서는 실제값과 예측값 간의 차이인 오차(Error)를 측정해야 합니다. 각 지표는 서로 다른 관점에서 모델의 성능을 평가하며, 비즈니스 요구사항에 따라 적절한 지표를 선택해야 합니다.

### 성능 지표별 특징과 계산 원리

**MAE (Mean Absolute Error)**
- 계산: `(|오차1| + |오차2| + ... + |오차n|) ÷ n`
- 특징: 평균적인 오차 크기, 직관적 해석, 이상값에 덜 민감
- 용도: 모델의 전반적인 실력 측정

**RMSE (Root Mean Square Error)**
- 계산: `√[(오차1² + 오차2² + ... + 오차n²) ÷ n]`
- 특징: 큰 오차에 민감, 안정성 중시
- 용도: 큰 실수를 피해야 하는 상황에서 활용

**MAPE (Mean Absolute Percentage Error)**
- 계산: `(1/n) × Σ |실제값 - 예측값| / |실제값| × 100%`
- 특징: 상대적 오차 비율, 규모 무관 비교 가능
- 용도: 시기별/제품별/모델별 상대적 성능 비교

### 시각화 방법별 활용 시나리오

```
시계열 분석 플로우:
시간 → 성능지표(MAE/RMSE/MAPE) → 선 그래프 → 성능 변화 추이 파악

관계 분석 플로우:
변수1 → 변수2 → 산점도 → 상관관계/패턴 발견

분포 분석 플로우:
오차값들 → 히스토그램 → 오차 분포 특성 파악
```

### Trade-offs 고려사항

**MAE 장점**:
- 해석이 직관적 ("평균적으로 X만큼 틀림")
- 이상값의 영향을 제한적으로 받음
- 비즈니스 임팩트 계산 용이

**MAE 단점**:
- 큰 오차의 위험성을 과소평가할 수 있음
- 모든 오차를 동등하게 취급

**RMSE 장점**:
- 큰 오차에 민감하여 리스크 관리에 유리
- 통계적으로 표준화된 지표

**RMSE 단점**:
- 이상값에 과도하게 민감할 수 있음
- MAE보다 해석이 어려움

**MAPE 장점**:
- 상대적 비교가 가능
- 규모가 다른 데이터 간 비교 용이
- 비즈니스 관점에서 이해하기 쉬움

**MAPE 단점**:
- 실제값이 0에 가까우면 계산 불가
- 작은 실제값에서 과도하게 큰 값 산출

## 🔍 주요개념

### 시각화 방법별 특징

- **선 그래프(Line Chart)**: 시간에 따른 성능 변화 추이, 모델 degradation 감지에 최적
- **산점도(Scatter Plot)**: 두 변수 간 관계 분석, 상관관계 및 패턴 발견
- **히스토그램(Histogram)**: 오차 분포 분석, 편향성 및 이상값 탐지

### 실전 적용

- **온라인 쇼핑몰**: 일별 매출 예측 모델의 주간 MAPE 추이로 계절성 영향 분석
- **재고 관리**: 제품별 MAPE 비교를 통한 예측 난이도 파악 및 리소스 배분
- **금융 서비스**: 주식 가격 예측에서 RMSE 중심 평가로 큰 손실 위험 최소화

## 📝 관련 문제

**Question:** A retail firm forecasts daily sales using a machine learning model. According to the company's brand manager, the model has been producing erroneous findings for the last three weeks. At the conclusion of each day, an AWS Glue task consolidates the forecasting input data with the actual daily sales data and the model's predictions. What should the machine learning team perform on the SageMaker Studio notebook to most properly show the model's degradation?

**Options:**

- A) Create a histogram of the daily sales over the last 3 weeks. In addition, create a histogram of the daily sales from before that period.
- B) Create a histogram of the model errors over the last 3 weeks. In addition, create a histogram of the model errors from before that period.
- C) Create a line chart with the weekly mean absolute error (MAE) of the model.
- D) Create a scatter plot of daily sales versus model error for the last 3 weeks. In addition, create a scatter plot of daily sales versus model error from before that period.

**정답: C) Create a line chart with the weekly mean absolute error (MAE) of the model.**

💡 추가 설명:

- **Option A** - 매출 분포만 보여주며 모델 성능과는 무관함
- **Option B** - 오차 분포는 보여주지만 시간에 따른 변화 추이를 파악하기 어려움
- **Option D** - 매출 규모에 따른 오차 패턴은 보여주지만 시간에 따른 성능 변화는 보여주지 못함
- **정답 C** - 시간에 따른 모델 성능 변화를 명확히 보여주어 언제부터 성능이 저하되었는지 파악 가능