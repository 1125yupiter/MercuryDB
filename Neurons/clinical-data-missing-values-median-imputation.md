---

title: clinical-data-missing-values-median-imputation
created: 2025-08-19
modified: 2025-08-19
tags:
- data-preprocessing/imputation
- statistics/median
- healthcare/clinical-trials
- outlier-handling/robust-methods
- missing-data/low-percentage
aliases: ["MAP imputation", "median imputation", "clinical missing data"]

---

# 임상 데이터 결측값 처리 - 이상치가 있는 환경에서의 중앙값 대체 전략

## 🎯 핵심 포인트

이상치가 존재하고 결측값이 1% 미만인 임상시험 데이터에서, 중앙값을 사용한 대체가 평균보다 더 강건하고 효과적인 결과를 제공할 수 있다.

## 📝 설명

### 중앙값 대체가 임상 데이터 전처리에 적합한 이유

임상시험 데이터는 생체 신호의 특성상 이상치를 포함하는 경우가 많으며, 특히 평균동맥압(MAP) 같은 생리학적 지표는 극값을 가질 수 있습니다. 중앙값은 분포의 중심 위치를 나타내면서도 이상치의 영향을 최소화하는 강건한 통계량입니다. 결측값 비율이 낮을 때(1% 미만) 중앙값 대체는 원본 데이터의 분포 특성을 잘 보존하면서도 편향을 최소화합니다.

### 데이터 처리 플로우

```
원본 임상 데이터
    ↓
이상치 및 분포 특성 분석
    ↓
결측값 패턴 확인 (<1% MAP 데이터)
    ↓
중앙값 계산 (이상치 영향 최소화)
    ↓
결측값을 중앙값으로 대체
    ↓
대체 후 분포 특성 검증
    ↓
최종 전처리된 데이터셋
```

### Trade-offs 고려사항

**중앙값 대체 장점**:
- 이상치에 대한 강건성 (robust to outliers)
- 분포의 중심 경향성 보존
- 낮은 결측 비율에서 편향 최소화
- 의료 데이터의 생리학적 범위 내 값 보장

**중앙값 대체 단점**:
- 데이터의 변동성(variance) 과소추정 가능성
- 극단적 분포에서는 대표성 부족
- 시계열 특성이나 개인별 패턴 무시

**평균 대체 장점**:
- 수학적 계산의 일관성
- 정규분포에서 최적 추정량

**평균 대체 단점**:
- 이상치에 민감하여 왜곡 가능성 높음
- 의료 데이터에서 비현실적 값 생성 위험
- 편향된 대체값으로 인한 분석 결과 왜곡

## 🔍 주요개념

### 중심경향 측도 비교

- **평균(Mean)**: 모든 값의 합을 개수로 나눈 값, 이상치에 민감
- **중앙값(Median)**: 정렬된 데이터의 중간값, 이상치에 강건
- **최빈값(Mode)**: 가장 자주 나타나는 값, 범주형 데이터에 적합

### 실전 적용

- **심혈관계 임상시험**: 혈압, 심박수 등 생체신호 데이터의 결측값 처리
- **약물 효능 연구**: 바이오마커 수치의 이상치 존재 시 안정적 대체
- **다기관 임상연구**: 측정 기기나 환경 차이로 인한 이상치 영향 최소화

## 📝 관련 문제

**Question:** In a clinical trial dataset that encompasses a variety of features including Mean Arterial Pressure (MAP), it's observed that the features exhibit low correlation with one another. The dataset is almost complete, with less than 1% of the MAP values missing. Aside from a few outliers, the MAP data is relatively uniformly distributed, and all other features are fully accounted for. Given these characteristics, which approach should be adopted to manage the missing MAP data most effectively?

**Options:**

- A) Fill in the missing MAP values with random noise to maintain the dataset's volume
- B) Impute the missing MAP values with the mean of the available MAP data, aiming for a central tendency measure
- C) Impute the missing MAP values using the median of the MAP data available, considering the distribution's characteristics and the presence of outliers
- D) Exclude the MAP column entirely from the dataset due to the incompleteness of its data

**정답: C) Impute the missing MAP values using the median of the MAP data available**

💡 추가 설명:

- **A) 랜덤 노이즈 대체** - 통계적 원리에 맞지 않으며 데이터 품질을 저하시켜 분석 결과의 신뢰성을 떨어뜨림
- **B) 평균값 대체** - 이상치가 존재하는 환경에서 평균은 왜곡되기 쉬워 부정확한 대체값을 생성할 위험이 높음
- **D) 컬럼 전체 제거** - 99% 이상의 유용한 정보를 버리는 것은 비효율적이며, 중요한 임상 지표를 잃게 됨

---