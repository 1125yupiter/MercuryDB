---

title: forecast-timeseries-accuracy-metrics
created: 2025-08-21
modified: 2025-08-21
tags:
- service/amazon-forecast
- metrics/weighted-quantile-loss
- data/timeseries
- evaluation/probabilistic
- comparison/sagemaker
aliases: ["amazon-forecast", "wql", "time-series-forecasting"]

---

# Amazon Forecast의 시계열 예측과 정확도 평가 메트릭

## 🎯 핵심 포인트

시간 순서가 있는 비즈니스 데이터를 예측해야 하는 경우 Amazon Forecast에서, 확률적 예측과 WQL 메트릭을 통해 불확실성을 포함한 정확도 평가를 할 수 있다.

## 📝 설명

### Amazon Forecast가 시계열 예측에 적합한 이유

Amazon Forecast는 시간 정보가 핵심인 데이터에 특화된 관리형 머신러닝 서비스입니다. 단순한 점 예측이 아닌 확률적 예측을 통해 "90% 확률로 80-120개 사이에서 팔릴 것"과 같은 불확실성 정보를 제공합니다. 시계열 데이터의 특성인 트렌드, 계절성, 주기성, 자기상관성을 자동으로 학습하며, CNN-QR, DeepAR+, Prophet, ARIMA 등 다양한 시계열 전용 알고리즘을 제공합니다.

### 아키텍처 플로우

```
Raw Time Series Data → Amazon Forecast
     ↓
Data Preprocessing (자동)
- 결측값 처리
- 이상치 탐지
- 시간 정렬 및 보간
- 계절성 분해
     ↓
Algorithm Selection (자동)
- CNN-QR, DeepAR+, Prophet, ARIMA 등
     ↓
Training & Hyperparameter Tuning (자동)
     ↓
Probabilistic Forecast Output
- P10, P25, P50, P75, P90 분위수
     ↓
Accuracy Evaluation with WQL
- 각 분위수별 Quantile Loss 계산
- 가중평균으로 종합 평가
```

### Trade-offs 고려사항

**Amazon Forecast 장점**:
- 완전 관리형 서비스로 즉시 사용 가능
- 시계열 전용 전처리 자동화
- 확률적 예측으로 리스크 관리 가능
- 빠른 개발 (1-2일)

**Amazon Forecast 단점**:
- 커스터마이징 제한적
- 표준 시계열 문제에만 적합
- 특수한 도메인 로직 반영 어려움

**SageMaker 장점**:
- 완전한 알고리즘 커스터마이징
- 최신 연구 모델 직접 구현
- 멀티모달 데이터 처리 가능
- 세밀한 실험 제어

**SageMaker 단점**:
- 긴 개발 시간 (2-4주)
- 시계열 ML 전문가 필요
- 인프라 관리 부담
- 높은 초기 투자 비용

## 🔍 주요개념

### 정확도 메트릭 비교

- **RMSE (Root Mean Squared Error)**: Linear Regression에서 사용하는 수치적/절대적 오차 표현. "평균적으로 50만원 틀렸다"
- **MAPE (Mean Absolute Percentage Error)**: 비율적/상대적 오차 표현. "평균적으로 8% 틀렸다". 비즈니스에서 이해하기 쉬움
- **WQL (Weighted Quantile Loss)**: Amazon Forecast의 핵심 메트릭. 확률적 예측의 품질을 종합 평가

### WQL (Weighted Quantile Loss) 메커니즘

Amazon Forecast는 단일 예측값이 아닌 확률 분포로 예측합니다:
- P10: 80개 (10% 확률로 이보다 적음)
- P50: 100개 (중앙값)
- P90: 120개 (90% 확률로 이보다 적음)

각 분위수별로 Quantile Loss를 계산하고, 비즈니스 중요도에 따른 가중치를 적용하여 최종 WQL을 산출합니다.

### 실전 적용

- **소매업**: 제품별 수요 예측으로 재고 최적화 (P90 기준 보수적 vs P25 기준 공격적)
- **금융**: 거래량 예측으로 리스크 관리 및 현금 흐름 계획
- **에너지**: 전력 수요 예측으로 발전 계획 및 그리드 관리

## 📝 관련 문제

**Question:** A retail company needs to forecast daily sales for 1000+ products across multiple stores. They want probabilistic forecasts with confidence intervals and automatic handling of seasonality. The ML team has limited time series expertise. Which approach would be most suitable?

**Options:**

- A) Build custom ARIMA models using SageMaker with manual hyperparameter tuning
- B) Use Amazon Forecast with automatic algorithm selection and WQL evaluation
- C) Implement Linear Regression with RMSE and MAPE metrics on EC2
- D) Deploy Prophet models manually on SageMaker endpoints
- E) Use traditional statistical methods with Excel forecasting

**정답: B) Use Amazon Forecast with automatic algorithm selection and WQL evaluation**

💡 추가 설명:

- **A) Custom ARIMA on SageMaker** - 시계열 전문가가 부족한 상황에서 수동 튜닝은 비효율적
- **C) Linear Regression** - 시계열 데이터의 시간적 패턴과 계절성을 처리할 수 없음
- **D) Manual Prophet on SageMaker** - 1000+ 제품에 대한 수동 배포는 관리 부담 과중
- **E) Excel forecasting** - 대규모 데이터와 복잡한 패턴 처리에 한계