---

title: model-degradation-sagemaker-monitoring
created: 2025-08-21
modified: 2025-08-21
tags:
- monitoring/model-performance
- service/sagemaker-studio
- analysis/time-series
- metric/mae
- visualization/line-chart
aliases: ["model-drift", "performance-degradation", "mae-monitoring"]

---

# ML 모델 성능 저하 모니터링 - SageMaker Studio에서 시계열 분석

## 🎯 핵심 포인트

모델의 성능 저하를 분석하는 경우 SageMaker Studio에서, 시간에 따른 MAE 변화를 선 그래프로 시각화하여 성능 저하 시점과 정도를 명확히 파악할 수 있다.

## 📝 설명

### SageMaker Studio가 모델 성능 모니터링에 적합한 이유

SageMaker Studio는 머신러닝 모델의 성능을 지속적으로 모니터링하고 분석하는 통합 환경을 제공합니다. 특히 모델의 성능 저하(Model Drift/Degradation)를 감지하고 분석하기 위해서는 시간에 따른 성능 지표의 변화를 추적할 수 있는 시각화가 필수적입니다.

### 아키텍처 플로우

```
실제 매출 데이터 → AWS Glue (일별 통합) → S3 저장소
예측 입력 데이터 ↗                         ↓
모델 예측 결과 ↗                    SageMaker Studio
                                        ↓
                               주간 MAE 계산 및 시각화
                                        ↓
                                성능 저하 패턴 분석
```

### Trade-offs 고려사항

**선 그래프 (Line Chart) 장점**:
- 시간에 따른 성능 변화 추이를 명확히 시각화
- 성능 저하 시작 시점을 정확히 파악 가능
- 주간/월간 집계로 노이즈 제거 및 트렌드 강조
- 계절성이나 주기적 패턴 식별 가능

**선 그래프 단점**:
- 개별 데이터 포인트의 세부 정보는 제한적
- 이상치(outlier)가 전체 트렌드에 미치는 영향 파악 어려움

**히스토그램 장점**:
- 오류 분포의 전체적인 형태 파악 가능
- 특정 기간 내 오류 값들의 빈도 분석

**히스토그램 단점**:
- 시간에 따른 변화 추이를 직접적으로 보여주지 못함
- 성능 저하 시점 파악이 어려움

## 🔍 주요개념

### 성능 지표 비교

- **MAE (Mean Absolute Error)**: 예측값과 실제값의 절대 오차 평균, 해석이 직관적
- **MSE (Mean Squared Error)**: 제곱 오차 평균, 큰 오차에 더 민감
- **RMSE (Root Mean Squared Error)**: MSE의 제곱근, 원본 단위와 동일

### 실전 적용

- **소매업 매출 예측**: 계절성과 프로모션 효과로 인한 성능 변화 모니터링
- **금융 서비스 신용 평가**: 경제 상황 변화에 따른 모델 성능 저하 감지
- **제조업 수요 예측**: 시장 트렌드 변화로 인한 예측 정확도 하락 추적

## 📝 관련 문제

**Question:** A retail firm forecasts daily sales using a machine learning (ML) model. According to the company's brand manager, the model has been producing erroneous findings for the last three weeks. At the conclusion of each day, an AWS Glue task consolidates the forecasting input data with the actual daily sales data and the model's predictions. The data is stored in Amazon S3 via the AWS Glue task. The company's machine learning team is analyzing the model's errors using an Amazon SageMaker Studio notebook. What should the machine learning team perform on the SageMaker Studio notebook to most properly show the model's degradation?

**Options:**

- A) Create a histogram of the daily sales over the last 3 weeks. In addition, create a histogram of the daily sales from before that period.
- B) Create a histogram of the model errors over the last 3 weeks. In addition, create a histogram of the model errors from before that period.
- C) Create a line chart with the weekly mean absolute error (MAE) of the model.
- D) Create a scatter plot of daily sales versus model error for the last 3 weeks. In addition, create a scatter plot of daily sales versus model error from before that period.

**정답: C) Create a line chart with the weekly mean absolute error (MAE) of the model**

💡 추가 설명:

- **옵션 A** - 실제 매출 분포만 보여주며 모델의 예측 성능과는 직접적 관련이 없음
- **옵션 B** - 오류 분포는 보여주지만 시간에 따른 성능 변화 추이를 파악하기 어려움
- **옵션 D** - 매출 규모와 오류 간의 상관관계는 분석할 수 있지만 시간적 변화는 보여주지 못함

---