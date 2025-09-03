---

title: outlier-sagemaker-datawrangler-preprocessing
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker-datawrangler
- technique/outlier-detection
- method/data-preprocessing
- usecase/income-prediction
- problem/extreme-outliers
aliases: ["data-wrangler", "outlier-detection", "preprocessing"]

---

# SageMaker Data Wrangler - 아웃라이어 탐지 및 데이터 전처리

## 🎯 핵심 포인트

선형 회귀 모델에서 극단적인 아웃라이어(억만장자 소득)를 처리해야 하는 경우 SageMaker Data Wrangler에서, 시각적 워크플로우를 통해 아웃라이어를 탐지하고 제거할 수 있다.

## 📝 설명

### SageMaker Data Wrangler가 아웃라이어 처리에 적합한 이유

SageMaker Data Wrangler는 데이터 준비와 전처리에 특화된 시각적 도구입니다. 코드 작성 없이 GUI 기반으로 아웃라이어를 탐지하고, 다양한 변환 기법을 적용할 수 있습니다. 특히 극단적인 값(억만장자 소득)이 포함된 데이터셋에서 일반 인구 대상 모델의 정확도를 향상시키는 데 효과적입니다. 통계적 분석, 시각화, 변환을 원스톱으로 제공하여 데이터 과학자가 탐색적 데이터 분석부터 전처리까지 효율적으로 수행할 수 있습니다.

### 아키텍처 플로우

```
Raw Data (억만장자 소득 포함) → Data Wrangler Import
    ↓
시각적 탐색 (박스플롯, 히스토그램) → 아웃라이어 탐지
    ↓
변환 적용 (제거/캡핑/로그변환) → 전처리된 데이터
    ↓
Feature Store 저장 → 모델 훈련 파이프라인
```

### Trade-offs 고려사항

**SageMaker Data Wrangler 장점**:
- GUI 기반 직관적 인터페이스로 코드 작성 불필요
- 박스플롯, 히스토그램 등 시각화를 통한 아웃라이어 식별
- 제거, 캡핑, 로그변환 등 다양한 처리 옵션
- 자동 코드 생성으로 재사용 가능한 전처리 파이프라인 구축

**SageMaker Data Wrangler 단점**:
- 대용량 데이터 처리 시 성능 제약
- 고급 통계 분석 기능 제한적
- 실시간 처리 불가

**SageMaker Feature Store 장점**:
- 피처 버전 관리 및 공유
- 온라인/오프라인 스토어 지원

**SageMaker Feature Store 단점**:
- 아웃라이어 탐지/제거 기능 없음
- 단순 저장소 역할로 전처리 기능 제한적

## 🔍 주요개념

### 아웃라이어 처리 기법 비교

- **완전 제거**: 극단값 데이터 포인트를 데이터셋에서 삭제
- **캡핑(Capping)**: 상한선/하한선을 설정하여 극단값을 임계값으로 대체
- **로그 변환**: 소득 분포를 정규화하여 극단값의 영향 완화
- **IQR 기반 필터링**: 사분위수 범위를 이용한 통계적 아웃라이어 탐지

### 실전 적용

- 소득 예측 모델에서 억만장자 데이터로 인한 모델 왜곡 방지
- 부동산 가격 예측에서 초고가 매물로 인한 편향 제거
- 의료비 예측 모델에서 극단적 치료비 케이스 처리

## 📝 관련 문제

**Question:** A data scientist is developing a linear regression model to forecast income using age among other variables. The dataset includes extreme outliers due to the inclusion of billionaires' incomes. To ensure the model accurately predicts income for the majority of the population, excluding billionaires, what is the most effective method for managing these outliers?

**Options:**

- A) Apply Amazon SageMaker Data Wrangler for detecting and handling outliers through its data preparation workflows
- B) Use Amazon SageMaker Feature Store to automatically filter out outlier data points before feeding them into the model
- C) Implement Amazon SageMaker Clarify to detect outliers in the training data and adjust model fairness accordingly
- D) Leverage Amazon SageMaker Model Monitor to identify and remove outliers in real-time during model inference
- E) Use Amazon SageMaker Processing Jobs with custom outlier detection algorithms

**정답: A) Amazon SageMaker Data Wrangler**

💡 추가 설명:

- **Feature Store (B)** - 피처 저장 및 관리용으로 아웃라이어 탐지/제거 기능 제공하지 않음
- **Clarify (C)** - 편향성 탐지와 설명가능성에 특화되어 데이터 전처리 기능 없음
- **Model Monitor (D)** - 배포 후 모델 성능 모니터링용으로 훈련 데이터 전처리와 무관
- **Processing Jobs (E)** - 커스텀 코딩이 필요하며 Data Wrangler보다 복잡함