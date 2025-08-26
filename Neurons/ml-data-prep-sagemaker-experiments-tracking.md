---
title: ml-data-prep-sagemaker-experiments-tracking
created: 2025-08-26
modified: 2025-08-26
tags:
- service/sagemaker-experiments
- technique/window-aggregation
- constraint/parameter-optimization
- usecase/feature-engineering
- method/experiment-tracking
aliases: ["ML-Experiments", "Data-Prep-Optimization", "Feature-Impact-Analysis"]

---

# ML 데이터 준비에서 SageMaker Experiments를 활용한 파라미터 최적화

## 🎯 핵심 포인트

복잡한 윈도우 집계 기법을 사용하여 피처 수와 샘플 크기가 모델 성능에 미치는 영향을 체계적으로 평가해야 하는 경우, AWS Glue보다 SageMaker Processing Job + SageMaker Experiments를 활용하면 실험 추적과 비교 분석을 효과적으로 수행할 수 있다.

## 📝 설명

### SageMaker Experiments가 ML 데이터 준비 최적화에 적합한 이유

SageMaker Experiments는 머신러닝 실험의 파라미터, 메트릭, 아티팩트를 자동으로 추적하고 비교할 수 있는 관리형 서비스입니다. 특히 데이터 준비 단계에서 다양한 변환 기법의 효과를 평가할 때, 실험 조건(피처 수, 샘플 크기, 윈도우 크기 등)과 결과를 체계적으로 관리할 수 있습니다.

**Window Aggregation Technique**: 시계열 데이터에서 특정 범위(윈도우)를 기준으로 집계 연산을 수행하는 기법으로, 롤링 윈도우, 확장 윈도우, 슬라이딩 윈도우 등이 있습니다. ML에서는 트렌드, 패턴, 이동평균 등을 피처로 생성하여 모델 성능을 향상시키는 데 사용됩니다.

### 아키텍처 플로우

```
실험 설계 → SageMaker Processing Job (PySpark + Window Aggregation) 
     ↓
파라미터 자동 추적 (SageMaker Experiments Tracker)
     ↓
다양한 조합 실험 (피처 수 × 샘플 크기 × 윈도우 크기)
     ↓  
메트릭 자동 수집 → 실험 결과 비교/시각화 → 최적 조합 선택
```

### Trade-offs 고려사항

**SageMaker Experiments + Processing Job 장점**:
- 자동화된 실험 추적 및 파라미터 관리
- 실험 간 성능 비교 및 시각화 제공
- ML 워크플로우에 최적화된 환경
- 재현성과 협업 지원

**SageMaker Experiments + Processing Job 단점**:
- AWS 생태계에 종속적
- 비용이 상대적으로 높을 수 있음
- 설정 복잡도가 있음

**AWS Glue 장점**:
- ETL 작업에 특화되어 효율적
- 서버리스로 관리 오버헤드 적음
- 대용량 데이터 처리에 최적화

**AWS Glue 단점**:
- 실험 추적 기능 제한적 (수동 관리 필요)
- ML 실험 비교와 시각화 부족
- 파라미터 영향 분석을 위한 체계적 도구 부재

## 🔍 주요개념

### 실험 추적에서 Parameter vs Metric

- **Parameter**: 실험자가 설정하는 입력 조건 (피처 수, 샘플 크기, 윈도우 크기 등)
- **Metric**: 실험 결과로 측정되는 값 (모델 정확도, F1 스코어, 처리 시간 등)

### 실전 적용

- **금융 서비스**: 고객별 거래 패턴 분석을 위한 롤링 윈도우 피처 생성 및 최적 윈도우 크기 결정
- **리테일**: 상품별 판매 트렌드 분석에서 피처 수와 예측 성능 간의 관계 분석
- **제조업**: 센서 데이터의 시계열 집계에서 샘플링 주기와 이상 탐지 성능 최적화

## 📝 관련 문제

**Question:** A machine learning expert wishes to construct a data preparation task that utilizes a PySpark script with complicated window aggregation techniques to generate training and testing data. The machine learning professional must assess the effect of the amount of features and sample size on model performance. Which strategy should the machine learning professional use when determining the model's optimal data transformations?

**Options:**

- A) Add an Amazon SageMaker Debugger hook to the script to capture key metrics. Run the script as an AWS Glue job.
- B) Add an Amazon SageMaker Experiments tracker to the script to capture key metrics. Run the script as an AWS Glue job.
- C) Add an Amazon SageMaker Debugger hook to the script to capture key parameters. Run the script as a SageMaker processing job.
- D) Add an Amazon SageMaker Experiments tracker to the script to capture key parameters. Run the script as a SageMaker processing job.

**정답: D) Amazon SageMaker Experiments tracker + SageMaker Processing Job**

💡 추가 설명:

- **옵션 A & C (SageMaker Debugger)** - Debugger는 모델 훈련 중 디버깅과 모니터링을 위한 도구로, 데이터 전처리 단계의 실험 추적에는 부적합
- **옵션 A & B (AWS Glue Job)** - Glue는 ETL 작업에 최적화되어 있지만, ML 실험의 체계적 추적과 비교 분석 기능이 제한적
- **핵심 구분점** - "파라미터의 영향을 평가"하는 것이 목적이므로, 실험 조건인 피처 수와 샘플 크기는 Parameter로 추적하고, 결과인 모델 성능은 Metric으로 측정해야 함