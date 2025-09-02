---

title: forecast-algorithm-mape-optimization
created: 2025-08-22
modified: 2025-08-22
tags:
- service/amazon-forecast
- metric/mape
- optimization/automl
- optimization/hpo
- problem/forecasting
aliases: ["forecast-mape", "amazon-forecast-accuracy", "forecast-optimization"]

---

# Amazon Forecast 알고리즘 선택과 MAPE 최적화

## 🎯 핵심 포인트

Amazon Forecast에서 예측기의 MAPE가 높은 경우 AutoML과 HPO를 활용하여 알고리즘 최적화와 하이퍼파라미터 튜닝을 통해 예측 정확도를 향상시킬 수 있다.

## 📝 설명

### Amazon Forecast가 시계열 예측에 적합한 이유

Amazon Forecast는 완전 관리형 시계열 예측 서비스로, 기계학습을 통해 고정확도 예측을 제공합니다. 6개의 내장 알고리즘(CNN-QR, DeepAR+, Prophet, NPTS, ARIMA, ETS)을 제공하며, AutoML 기능을 통해 최적 알고리즘을 자동 선택할 수 있습니다.

### 알고리즘 선택 방식

```
데이터셋 준비 → 예측기 생성 → 알고리즘 선택 → 훈련 → 예측 생성
                ↓
        [수동 선택] vs [AutoML]
        ↓                ↓
    AlgorithmArn       PerformAutoML=true
    지정              (자동 최적화)
        ↓                ↓
    HPO 선택적        HPO 자동 적용
    적용 가능
```

### Trade-offs 고려사항

**AutoML 장점**:
- 최적 알고리즘 자동 선택으로 정확도 향상
- P10, P50, P90 quantile 기반 객관적 평가
- 도메인 지식 없이도 고성능 모델 구축 가능

**AutoML 단점**:
- 훈련 시간 증가 (모든 알고리즘 테스트)
- 비용 상승 (더 많은 컴퓨팅 리소스 사용)
- 알고리즘별 세부 튜닝 제한

**수동 선택 장점**:
- 빠른 훈련 시간
- 특정 알고리즘에 대한 세부 제어
- 비용 효율성

**수동 선택 단점**:
- 도메인 전문지식 필요
- 최적이 아닌 알고리즘 선택 가능성
- 수동 하이퍼파라미터 튜닝 필요

## 🔍 주요개념

### MAPE vs 정확도 관계

- **MAPE (Mean Absolute Percentage Error)**: 값이 클수록 예측 오차가 크고 정확도가 낮음
- **정확도**: MAPE가 낮을수록 예측 정확도가 높음
- **목표**: MAPE 최소화를 통한 예측 정확도 최대화

### 실전 적용

- **AutoML 활용**: 여러 알고리즘 중 최적 성능 자동 선택
- **HPO 적용**: 선택된 알고리즘의 하이퍼파라미터 자동 최적화
- **Feature Engineering**: 적절한 FeaturizationMethodName 선택으로 데이터 품질 향상

## 📝 관련 문제

**Question:** A logistics firm uses Amazon Forecast to predict inventory requirements. The predictor's MAPE is higher than human forecasters. Which CreatePredictor API modifications could help reduce MAPE and improve accuracy?

**Options:**

- A) Set PerformAutoML to true
- B) Set ForecastHorizon to 4 (for 4-month prediction)
- C) Set ForecastFrequency to W for weekly
- D) Set PerformHPO to true
- E) Set FeaturizationMethodName to filling

**정답: A, D) AutoML과 HPO 활성화**

💡 추가 설명:

- **A (PerformAutoML=true)** - 모든 알고리즘을 평가하여 최적 성능 알고리즘 자동 선택, MAPE 감소
- **D (PerformHPO=true)** - 하이퍼파라미터 최적화를 통해 모델 성능 향상, MAPE 감소
- **B (ForecastHorizon=4)** - 예측 기간이 길수록 부정확해짐, MAPE 증가 요인
- **C (ForecastFrequency=W)** - 월간 데이터를 주간으로 변환 시 데이터 불일치 가능성
- **E (filling)** - 단순한 결측값 처리로 더 정교한 특성화 방법 대비 성능 저하