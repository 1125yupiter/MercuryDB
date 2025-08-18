---

title: outlier-sagemaker-clarify-bias
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker-clarify
- technique/bias-detection
- method/explainability
- usecase/fairness-analysis
- concept/shap-analysis
aliases: ["clarify", "bias-detection", "explainability"]

---

# SageMaker Clarify - 편향성 탐지 및 설명가능성 분석

## 🎯 핵심 포인트

머신러닝 모델의 편향성 탐지와 예측 설명이 필요한 경우 SageMaker Clarify에서, 데이터 및 모델의 공정성을 분석하고 SHAP 기반 설명가능성을 제공할 수 있다.

## 📝 설명

### SageMaker Clarify가 편향성 탐지에 적합한 이유

SageMaker Clarify는 머신러닝 모델의 투명성과 공정성을 보장하기 위한 전용 서비스입니다. 훈련 전후 데이터의 편향성을 정량적으로 측정하고, SHAP(SHapley Additive exPlanations) 알고리즘을 통해 모델의 예측 근거를 설명합니다. 아웃라이어 제거보다는 모델의 윤리성과 설명가능성에 초점을 맞춘 도구입니다.

### 아키텍처 플로우

```
훈련 데이터 → Pre-training Bias 분석 → 모델 훈련 
    ↓
Post-training Bias 분석 → SHAP 설명가능성 분석 
    ↓
편향성 보고서 생성 → 공정성 개선 권장사항
```

### Trade-offs 고려사항

**SageMaker Clarify 장점**:
- 포괄적인 편향성 메트릭 제공 (CI, DPL, KL, DAR 등)
- SHAP 기반 피처 중요도 분석
- 시각화된 보고서 자동 생성
- 규제 준수 지원

**SageMaker Clarify 단점**:
- 아웃라이어 탐지/제거 기능 없음
- 데이터 전처리 기능 제한적
- 편향성 분석에만 특화됨

**SageMaker Data Wrangler 장점**:
- 포괄적인 데이터 전처리 도구
- 아웃라이어 탐지 및 처리 기능
- 시각적 데이터 탐색 인터페이스
- 코드 생성 자동화

**SageMaker Data Wrangler 단점**:
- 편향성 분석 기능 없음
- 설명가능성 분석 불가

## 🔍 주요개념

### 편향성 메트릭 비교

- **Pre-training Bias**: 훈련 데이터 자체의 편향성 (CI, DPL, KL, CDD)
- **Post-training Bias**: 모델 예측 결과의 편향성 (DAR, DI, TE, DPPL)
- **SHAP Values**: 각 피처가 개별 예측에 미치는 기여도
- **Partial Dependence**: 특정 피처 변화가 전체 예측에 미치는 영향

### 실전 적용

- 채용 시스템에서 성별/인종 기반 차별 탐지
- 대출 승인 모델의 공정성 검증 및 규제 준수
- 의료 진단 AI의 설명가능성 확보 및 신뢰성 향상

## 📝 관련 문제

**Question:** A data scientist is developing a linear regression model to predict income using age and other variables. The dataset includes extreme outliers due to billionaire incomes. To ensure the model accurately predicts for the general population, what is the most effective method for handling these outliers?

**Options:**

- A) Apply Amazon SageMaker Data Wrangler for detecting and handling outliers through its data preparation workflows
- B) Use Amazon SageMaker Feature Store to automatically filter out outlier data points before feeding them into the model
- C) Implement Amazon SageMaker Clarify to detect outliers in the training data and adjust model fairness accordingly
- D) Leverage Amazon SageMaker Model Monitor to identify and remove outliers in real-time during model inference
- E) Use Amazon SageMaker Processing Jobs with custom outlier detection algorithms

**정답: A) Amazon SageMaker Data Wrangler**

💡 추가 설명:

- **SageMaker Clarify (C)** - 편향성 탐지와 설명가능성에 특화되어 아웃라이어 제거 기능 없음
- **Feature Store (B)** - 피처 저장소로 아웃라이어 탐지/제거 기능 제공하지 않음
- **Model Monitor (D)** - 배포 후 모델 성능 모니터링용으로 훈련 데이터 전처리와 무관
- **Processing Jobs (E)** - 가능하지만 Data Wrangler가 더 직관적이고 효율적임