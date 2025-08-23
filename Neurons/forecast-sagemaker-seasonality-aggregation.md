---

title: forecast-sagemaker-seasonality-aggregation
created: 2025-08-23
modified: 2025-08-23
tags:
- problem/forecasting
- service/sagemaker
- constraint/seasonal-data
- technique/data-preparation
- usecase/retail-sales
aliases: ["forecast-issues", "seasonality-problem", "data-aggregation"]

---

# 소매 판매 예측에서 계절성 부족과 데이터 집계 문제 해결

## 🎯 핵심 포인트

신규 매장이 과반수인 소매 판매 예측 모델 구축 시, 계절성 데이터 부족과 주간 집계된 데이터로 인해 일일 예측 정확도가 저하될 수 있으며, 유사 매장 연결과 일일 데이터 요청으로 해결할 수 있다.

## 📝 설명

### SageMaker 예측 모델이 소매 판매 예측에 직면하는 주요 문제

소매업체의 일일 판매량 예측 모델 구축 시 두 가지 핵심 데이터 품질 문제가 성능에 심각한 영향을 미친다. 첫째, 매장의 절반 이상이 6개월 미만의 신규 매장으로 계절성 패턴을 학습할 수 없다. 둘째, 일일 예측이 목표임에도 주간 집계 데이터를 사용하여 요일별 패턴을 놓치게 된다.

### 아키텍처 플로우

```
원본 일일 판매 데이터 (S3)
         ↓
주간 집계 처리 (현재 문제)
         ↓
계절성 부족한 신규 매장 데이터
         ↓
SageMaker 예측 모델 학습
         ↓
부정확한 일일 예측 결과

개선된 플로우:
일일 판매 데이터 유지 → 유사 매장 카테고리 추가 → 충분한 계절성 확보 → 정확한 예측
```

### Trade-offs 고려사항

**일일 데이터 사용의 장점**:
- 요일별 패턴 학습 가능 (주말 vs 평일 판매 차이)
- 일일 예측 목표에 부합하는 세밀한 예측
- 계절성 및 트렌드 변화의 더 정확한 포착

**일일 데이터 사용의 단점**:
- 데이터 노이즈 증가로 인한 복잡성
- 더 많은 저장공간과 처리 시간 필요

**카테고리별 매장 그룹화 장점**:
- 신규 매장의 계절성 패턴 학습 가능
- 유사한 특성을 가진 매장들의 경험 활용
- 콜드 스타트 문제 완화

**카테고리별 매장 그룹화 단점**:
- 매장별 고유한 특성 희석 가능성
- 적절한 카테고리 정의의 복잡성

## 🔍 주요개념

### 예측 모델의 핵심 데이터 요구사항

- **계절성 데이터**: 최소 2-3년의 데이터로 연간 패턴 학습 필요
- **시간 해상도**: 예측 목표와 동일한 세분화 수준의 데이터 사용
- **제로 값 처리**: 실제 판매가 없었던 경우도 학습 데이터에 포함

### 실전 적용

- 신규 매장은 지역, 매장 크기, 고객층 등으로 카테고리화하여 기존 매장 데이터 활용
- 주간 집계 대신 일일 원본 데이터를 사용하여 요일별 패턴 학습
- 판매가 0인 경우도 명시적으로 포함하여 수요 없음도 하나의 패턴으로 학습

## 📝 관련 문제

**Question:** A business distributes wholesale apparel to thousands of retail locations. A data scientist must develop a model that forecasts each item's daily sales volume in each retailer. The data scientist learns that over half of the shops are less than six months old. Weekly sales data is quite stable. Weekly aggregates of daily data from the database were created, and weeks with no sales were excluded from the present dataset. Amazon S3 stores five years' worth of sales data (100 MB). Which variables will have a negative influence on the performance of the forecast model being constructed, and what mitigation measures can the data scientist take? (Select two.)

**Options:**

- A) Detecting seasonality for the majority of stores will be an issue. Request categorical data to relate new stores with similar stores that have more historical data.
- B) The sales data does not have enough variance. Request external sales data from other industries to improve the model's ability to generalize.
- C) Sales data is aggregated by week. Request daily sales data from the source database to enable building a daily model.
- D) The sales data is missing zero entries for item sales. Request that item sales data from the source database include zero entries to enable building the model.
- E) Only 100 MB of sales data is available in Amazon S3. Request 10 years of sales data, which would provide 200 MB of training data for the model.

**정답: A) & C)**

💡 추가 설명:

- **Option B** - 타 업계 데이터는 소매 의류업과 패턴이 달라 도움이 되지 않음
- **Option D** - 제로 엔트리 누락은 문제이지만 주요 성능 저하 요인은 아님
- **Option E** - 100MB 데이터는 충분하며, 단순 용량 확장보다 데이터 품질이 중요