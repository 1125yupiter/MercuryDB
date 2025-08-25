---

title: missing-data-supervised-imputation-census
created: 2025-08-18 
modified: 2025-08-18 
tags:
- problem/missing-data
- technique/supervised-imputation
- constraint/high-accuracy
- dataset/census-data
- method/predictive-modeling
aliases: ["missing-value-imputation", "supervised-imputation", "census-data-processing"]

---

# 결측값 처리 - 지도학습 기반 추정이 인구조사 데이터에서 효과적인 이유

## 🎯 핵심 포인트

상관관계가 있는 여러 피처를 포함한 인구조사 데이터에서 나이 컬럼의 10% 결측값이 있는 경우, 모델 예측 정확도 극대화를 위해서는 나머지 컬럼들을 활용한 보조 지도학습 모델을 개발하여 결측값을 추정할 수 있다.

## 📝 설명

### 지도학습 기반 결측값 추정이 인구조사 데이터에 적합한 이유

인구조사 데이터의 특성상 나이는 교육 수준, 직업, 소득, 결혼 상태, 거주지역 등 다른 변수들과 강한 상관관계를 가집니다. 이러한 피처 간 내재된 관계를 활용하여 별도의 예측 모델을 구축하면, 단순한 통계적 방법보다 훨씬 정확한 나이 추정이 가능합니다.

보조 지도학습 모델은 완전한 데이터를 가진 행들을 학습 데이터로 활용하여, 나이를 목적 변수(target variable)로 하고 나머지 피처들을 입력 변수로 하는 회귀 모델을 구축합니다. 이후 학습된 모델을 사용하여 나이가 결측된 행들의 값을 예측합니다.

### 결측값 처리 플로우

```
원본 데이터셋 (90% 완전한 나이 정보)
        ↓
완전한 데이터 추출 → 보조 지도학습 모델 학습
        ↓                    ↓
결측 데이터 추출 → 학습된 모델로 나이 예측
        ↓                    ↓
예측된 나이 값 → 원본 데이터셋에 결측값 대체
        ↓
완성된 데이터셋 → 최종 예측 모델 학습
```

### Trade-offs 고려사항

**지도학습 기반 추정 장점**:

- 피처 간 상관관계를 최대한 활용하여 정확한 추정
- 도메인 지식을 반영한 지능적 결측값 처리
- 최종 모델의 예측 성능 향상에 직접적 기여
- 나이라는 중요한 예측 변수 보존

**지도학습 기반 추정 단점**:

- 추가적인 모델 개발 및 학습 시간 소요
- 보조 모델의 성능에 따라 추정 품질 좌우
- 복잡성 증가로 인한 구현 난이도 상승

**평균값 대입 장점**:

- 구현이 간단하고 빠름
- 전체 데이터 분포의 중심값 유지

**평균값 대입 단점**:

- 피처 간 관계 완전히 무시
- 분산 감소로 인한 모델 성능 저하
- 나이 분포의 다양성 손실

**컬럼 제거 장점**:

- 결측값 문제 완전 해결
- 데이터 품질 우려 제거

**컬럼 제거 단점**:

- 중요한 예측 변수 완전 손실
- 모델 예측력 크게 감소
- 10% 결측은 제거하기엔 아까운 수준

## 🔍 주요개념

### 결측값 처리 방법 비교

- **완전 사례 분석(Complete Case Analysis)**: 결측값이 있는 행 전체 제거, 단순하지만 정보 손실 큼
- **평균/중위수 대입(Mean/Median Imputation)**: 통계값으로 일괄 대체, 분산 감소 문제
- **다중 대입(Multiple Imputation)**: 여러 번 추정하여 불확실성 반영, 통계적으로 엄밀함
- **예측 기반 대입(Predictive Imputation)**: 머신러닝 모델로 추정, 높은 정확도 가능

### 실전 적용

- **고객 분석**: 나이별 구매 패턴 분석 시 나이 정보가 핵심적이므로 정확한 추정 필요
- **신용 평가**: 나이-소득-직업 간 상관관계를 활용한 신용도 예측 모델에서 중요
- **의료 데이터**: 나이와 다른 건강 지표들 간 관계를 고려한 질병 예측 모델 구축

## 📝 관련 문제

**Question:** In a dataset derived from census information, which comprises several correlated feature columns, the column indicating a person's age is incomplete, with 10% of its values missing. Considering the goal of maximizing the predictive accuracy of a model developed using this dataset, which strategy should be employed to address the missing values in the age column?

**Options:**

- A) Eliminate the age column entirely from the dataset due to its incomplete nature
- B) Assign random values to the missing entries in the age column
- C) Impute the missing age values by calculating and using the mean age derived from the dataset's complete age entries
- D) Develop an auxiliary supervised learning model that utilizes the remaining columns to predict the missing age values, subsequently applying this model to estimate the missing entries

**정답: D) Develop an auxiliary supervised learning model**

💡 추가 설명:

- **A) 컬럼 제거** - 나이는 인구조사 데이터에서 가장 중요한 예측 변수 중 하나로, 제거 시 모델 성능이 크게 저하됨
- **B) 랜덤 값 할당** - 의미없는 노이즈를 추가하여 모델 학습을 방해하고 예측 정확도를 떨어뜨림
- **C) 평균값 대입** - 단순하지만 나이와 다른 변수들 간의 복잡한 관계를 전혀 고려하지 못함

---