---

title: forecast-demand-forecasting-horizon
created: 2025-08-20
modified: 2025-08-20
tags:
- service/forecast
- problem/forecasting
- constraint/horizon-required
- technique/feature-engineering
- usecase/demand-prediction
aliases: ["AWS Forecast", "time-series", "demand forecasting"]

---

# Amazon Forecast를 활용한 소매업 수요 예측

## 🎯 핵심 포인트

소매업체가 지역별 제품 수요를 예측하는 경우 Amazon Forecast에서, 예측 기간(forecasting horizon) 정의와 외부 데이터 제공이 필수적으로 요구된다.

## 📝 설명

### Amazon Forecast가 시계열 수요 예측에 적합한 이유

Amazon Forecast는 시계열 데이터 예측을 위해 특별히 설계된 완전 관리형 서비스입니다. 과거 판매 데이터, 제품 정보, 고객 인구통계 등의 복합적인 데이터를 활용하여 미래 수요를 예측할 수 있습니다.

**핵심 특징:**
- 자동 데이터 전처리 (결측값, 이상치, 범주형 변수 처리)
- 다양한 예측 알고리즘 자동 선택 및 최적화
- 대용량 데이터셋 처리 가능
- 외부 요인을 고려한 예측 정확도 향상

### 아키텍처 플로우

```
과거 판매 데이터 → Amazon Forecast → 자동 전처리
     ↓                                        ↓
외부 데이터 제공 → 예측 기간 설정 → 모델 훈련 → 수요 예측 결과
(휴일, 프로모션,              (4주, 12주 등)
 날씨, 경제지표)
```

### 필수 요구사항 vs 자동 처리

**사용자가 반드시 정의해야 하는 것:**

- **예측 기간(Forecasting Horizon)**: 얼마나 먼 미래까지 예측할지 명시
  - 예: 앞으로 4주간의 주간 수요
  - 예: 앞으로 6개월간의 월별 수요
- **외부 데이터 제공**: 정확도 향상을 위한 추가 정보
  - 휴일/이벤트 데이터 (크리스마스, 추석)
  - 프로모션 정보 (할인 이벤트)
  - 날씨 데이터 (계절성 제품용)
  - 경제 지표 (소비자 신뢰지수 등)

**Amazon Forecast가 자동으로 처리하는 것:**

- 결측값(누락된 데이터) 채우기
- 이상치(outlier) 감지 및 처리
- 데이터 공백 메우기
- 범주형 → 수치형 데이터 변환
- 피처 엔지니어링 및 선택
- 데이터 스케일링

### Trade-offs 고려사항

**Amazon Forecast 장점**:

- 완전 관리형 서비스로 인프라 관리 불필요
- 자동 데이터 전처리 및 피처 엔지니어링
- 다양한 알고리즘 자동 선택 및 앙상블
- 대용량 데이터 처리 가능

**Amazon Forecast 제약사항**:

- 예측 기간을 사전에 정의해야 함
- 최적 정확도를 위해서는 외부 데이터 필요
- 시계열 데이터에 특화 (다른 ML 문제에는 부적합)
- 실시간 예측보다는 배치 예측에 최적화

**SageMaker 장점**:

- 더 유연한 모델 커스터마이징 가능
- 다양한 ML 알고리즘 선택권
- 실시간 엔드포인트 지원

**SageMaker 단점**:

- 수동 데이터 전처리 필요
- 시계열 전용 기능 부족
- 더 많은 ML 전문 지식 요구

## 🔍 주요개념

### 핵심 개념 구분

- **Forecasting Horizon**: 모델이 예측할 미래 시간 범위 (4주, 12주, 6개월 등)
- **Target Time Series**: 예측하고자 하는 주요 지표 (주간 판매량, 일별 수요 등)
- **Related Time Series**: 예측 정확도 향상을 위한 외부 시계열 데이터
- **Item Metadata**: 제품별 정적 정보 (카테고리, 브랜드, 가격대 등)

### 실전 적용

- **소매업체**: 지역별 제품 수요 예측으로 재고 최적화
- **제조업체**: 원자재 수요 예측으로 공급망 관리 효율화  
- **에너지 업계**: 전력 수요 예측으로 발전량 계획 수립

## 📝 관련 문제

**Question:** A retail company wants to forecast the weekly demand for its products in different regions. They have historical sales data for the past three years, including the product category, price, promotion information, and customer demographics. They plan to use Amazon Forecast to train and deploy the machine learning model. Which of the following statements is correct about the Amazon Forecast service in this scenario?

**Options:**

- A) Amazon Forecast requires the user to manually preprocess the data before training the machine learning model
- B) Amazon Forecast does not support time-series forecasting and cannot handle large datasets  
- C) Amazon Forecast requires the user to define the forecasting horizon and provide external data to improve the accuracy of the forecast
- D) Amazon Forecast can automatically handle feature engineering and selection based on the provided data
- E) Amazon Forecast only works with real-time streaming data

**정답: C) Amazon Forecast requires the user to define the forecasting horizon and provide external data to improve the accuracy of the forecast**

💡 추가 설명:

- **선택지 A** - Amazon Forecast는 자동 전처리 기능을 제공하므로 수동 전처리가 불필요
- **선택지 B** - Amazon Forecast는 시계열 예측 전용 서비스이며 대용량 데이터 처리 가능
- **선택지 D** - 부분적으로 맞지만, 사용자가 타겟 시계열과 관련 데이터를 명시적으로 제공해야 함
- **선택지 E** - 배치 데이터로 훈련하며 실시간 스트리밍 전용 서비스가 아님