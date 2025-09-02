---

title: deepar-forecast-calibration-coverage
created: 2025-08-22
modified: 2025-08-22
tags:
- service/sagemaker-deepar
- method/probabilistic-forecast
- evaluation/coverage-score
- calibration/distributional
- algorithm/deepar
aliases: ["deepar-calibration", "coverage-score", "forecast-calibration"]

---

# GluonTS DeepAR 모델의 분포 예측 캘리브레이션 평가

## 🎯 핵심 포인트

분포 예측 모델의 캘리브레이션을 평가할 때 Coverage Score가 해당 분위수와 근사하게 일치하는 경우, 모델이 올바르게 캘리브레이션되었다고 판단할 수 있다.

## 📝 설명

### GluonTS DeepAR가 분포 예측에 적합한 이유

GluonTS DeepAR는 Amazon SageMaker에서 제공하는 확률적 시계열 예측 모델로, 단일 점 예측이 아닌 전체 확률 분포를 예측합니다. 이를 통해 불확실성을 정량화하고 다양한 분위수에서의 예측값을 제공할 수 있어, 리스크 관리와 의사결정에 매우 유용합니다.

### Coverage Score 평가 메커니즘

```
실제 관측값 → 예측 분포와 비교 → Coverage Score 계산
   ↓
분위수별 Coverage Score 분석
   ↓
캘리브레이션 상태 판단
```

**Coverage Score 계산 과정:**
1. 특정 분위수(예: 0.5, 0.9)에서 예측값 추출
2. 실제 관측값이 해당 분위수 이하에 위치하는 비율 계산
3. 이론적 기댓값과 비교하여 캘리브레이션 상태 평가

### Trade-offs 고려사항

**DeepAR 분포 예측 장점:**
- 불확실성 정량화 가능
- 다양한 분위수에서 예측값 제공
- 복잡한 시계열 패턴 학습 가능
- 확률적 예측을 통한 리스크 평가

**DeepAR 분포 예측 단점:**
- 캘리브레이션 검증 복잡성
- 해석이 어려운 확률 분포
- 계산 비용 증가
- 단순한 점 예측 대비 복잡한 평가 지표

**단순 점 예측 장점:**
- 직관적이고 해석하기 쉬움
- 빠른 계산과 평가
- 명확한 성능 지표 (MSE, MAE 등)

**단순 점 예측 단점:**
- 불확실성 정보 부족
- 리스크 평가 불가능
- 의사결정 지원 정보 제한

## 🔍 주요개념

### Coverage Score vs 캘리브레이션 상태

- **올바른 캘리브레이션**: Coverage Score ≈ 해당 분위수 (예: 0.5 분위수에서 0.48~0.52)
- **과소 캘리브레이션**: Coverage Score < 해당 분위수 (예: 0.5 분위수에서 0.3)
- **과대 캘리브레이션**: Coverage Score > 해당 분위수 (예: 0.5 분위수에서 0.7)

### 실전 적용

- **금융 리스크 관리**: VaR(Value at Risk) 계산을 위한 분위수별 손실 예측
- **재고 최적화**: 수요 예측 시 안전재고 수준 결정을 위한 분위수 활용
- **전력 수요 예측**: 피크 수요 대비를 위한 상위 분위수 예측값 활용

## 📝 관련 문제

**Question:** A data scientist is assessing a GluonTS DeepAR model on Amazon SageMaker. According to the assessment criteria for the test set, the coverage score is 0.489 at the 0.5 and 0.889 at the 0.9 quantiles, respectively. What can the data scientist properly infer about the test set's distributional forecast?

**Options:**

- A) The coverage scores indicate that the distributional forecast is poorly calibrated. These scores should be approximately equal to each other at all quantiles.
- B) The coverage scores indicate that the distributional forecast is poorly calibrated. These scores should peak at the median and be lower at the tails.
- C) The coverage scores indicate that the distributional forecast is correctly calibrated. These scores should always fall below the quantile itself.
- D) The coverage scores indicate that the distributional forecast is correctly calibrated. These scores should be approximately equal to the quantile itself.

**정답: D) The coverage scores indicate that the distributional forecast is correctly calibrated. These scores should be approximately equal to the quantile itself.**

💡 추가 설명:

- **옵션 A** - Coverage Score는 분위수별로 달라야 하며, 모든 분위수에서 동일할 필요 없음
- **옵션 B** - Coverage Score 패턴에 대한 잘못된 이해. 중앙값에서 피크를 가져야 한다는 것은 부정확
- **옵션 C** - "항상 분위수보다 낮아야 한다"는 조건은 부정확. 근사적으로 같아야 함
- **실제 해석** - 0.489 ≈ 0.5, 0.889 ≈ 0.9로 각각 매우 근접하여 올바른 캘리브레이션 상태