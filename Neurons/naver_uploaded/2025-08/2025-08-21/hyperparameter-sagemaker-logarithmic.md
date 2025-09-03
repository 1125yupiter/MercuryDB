---

title: hyperparameter-sagemaker-logarithmic
created: 2025-08-17
modified: 2025-08-17
tags:
- service/sagemaker
- technique/hyperparameter
- optimization/scaling
- constraint/cost-sensitive
- method/tuning
aliases: ["hpo-log-scaling", "sagemaker-tuning", "hyperparameter-optimization"]

---

# SageMaker 하이퍼파라미터 튜닝에서 로그 스케일링을 통한 최적화

## 🎯 핵심 포인트

학습률(learning rate)과 같이 여러 자릿수에 걸친 하이퍼파라미터를 튜닝하는 경우 SageMaker에서, ContinuousParameterRanges의 스케일링 타입을 Logarithmic으로 설정하고 최소값을 0보다 큰 값으로 조정하면 튜닝 시간과 비용을 크게 단축할 수 있다.

## 📝 설명

### SageMaker 자동 모델 튜닝이 하이퍼파라미터 최적화에 적합한 이유

SageMaker는 기본적으로 하이퍼파라미터 값의 균등 분포를 가정하고 선형 스케일링을 사용하여 검색 범위에서 값을 선택합니다. 하지만 학습률처럼 일반적인 값이 여러 자릿수에 걸쳐 있고 균등하게 분포되지 않는 하이퍼파라미터의 경우, 이 방법이 가장 효과적이지 않을 수 있습니다.

### 아키텍처 플로우

```
하이퍼파라미터 튜닝 프로세스:

Linear Scaling (기본)
범위: 0.0001 → 1.0
분포: [0.0001...0.1] (10%) → [0.1...1.0] (90%)
결과: 대부분의 예산이 큰 값에 집중

↓

Logarithmic Scaling (최적화)
범위: 0.0001 → 1.0
분포: 각 자릿수별 균등 분포
결과: 전체 범위에서 효율적 탐색

↓

튜닝 시간 및 비용 단축
```

### Trade-offs 고려사항

**Logarithmic Scaling 장점**:
- 여러 자릿수에 걸친 범위를 균등하게 탐색
- 작은 학습률 값들이 충분히 탐색됨
- 튜닝 시간과 비용 대폭 절약
- 전체 범위에서 더 나은 샘플링 제공

**Logarithmic Scaling 단점**:
- 0보다 큰 값에서만 작동
- 설정이 복잡할 수 있음
- 모든 하이퍼파라미터에 적합하지 않음

**Linear Scaling 장점**:
- 설정이 간단함
- 모든 값 범위에서 작동
- 균등하게 분포된 파라미터에 적합

**Linear Scaling 단점**:
- 학습률 같은 로그 스케일 파라미터에 비효율적
- 중요한 작은 값들이 충분히 탐색되지 않음
- 튜닝 시간과 비용이 많이 소요됨

## 🔍 주요개념

### 스케일링 타입 비교

- **Linear Scaling**: 균등 분포를 가정하며, 값들이 선형적으로 분포된 파라미터에 적합
- **Logarithmic Scaling**: 로그 분포를 가정하며, 여러 자릿수에 걸친 파라미터에 적합
- **Auto Scaling**: SageMaker가 자동으로 스케일링 방법을 결정하지만, 초기에는 선형 스케일링을 가정

### 실전 적용

- **학습률 튜닝**: 0.0001 ~ 1.0 범위에서 로그 스케일링으로 효율적 탐색
- **정규화 파라미터**: L1, L2 정규화 계수 같이 작은 값이 중요한 경우
- **배치 크기**: 2의 거듭제곱으로 증가하는 특성을 가진 파라미터

## 📝 관련 문제

**Question:** A Machine Learning Specialist is optimizing values for a model's learning rate using Amazon SageMaker automatic model tuning. The specified range of values for the learning rate is between .0001 and 1.0. The tuning time was more expensive and slower than expected. Which method could the Specialist apply to shorten the amount of time spent on succeeding tuning jobs?

**Options:**

- A) Set the scaling type of the ContinuousParameterRanges parameter to Logarithmic and adjust the desired minimum learning rate into a value lesser than 0.
- B) Set the scaling type of the ContinuousParameterRanges parameter to Logarithmic and adjust the desired minimum learning rate into a value greater than 0.
- C) Set the scaling type of the ContinuousParameterRanges parameter to Auto and adjust the desired minimum learning rate to 0.
- D) Set the scaling type of the ContinuousParameterRanges parameter to Linear and use a narrower range of values for the learning rate.

**정답: B) Set the scaling type of the ContinuousParameterRanges parameter to Logarithmic and adjust the desired minimum learning rate into a value greater than 0.**

💡 추가 설명:

- **Option A** - 로그 스케일링은 0보다 큰 값에서만 작동하므로 0보다 작은 값은 사용할 수 없음
- **Option C** - 최소값을 0으로 설정하면 SageMaker가 로그 스케일링을 선택하지 않을 가능성이 높음
- **Option D** - 범위를 좁히면 튜닝 속도는 빨라지지만 최적해를 찾을 가능성이 감소함