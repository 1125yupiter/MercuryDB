---

title: missing-value-mice-categorical
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/missing-values
- technique/mice
- datatype/categorical
- preprocessing/imputation
- quality/data-integrity
aliases: ["MICE", "Multiple Imputation", "결측값처리", "범주형데이터"]

---

# 범주형 피처 결측값 처리 - MICE 기법을 활용한 데이터 무결성 보장

## 🎯 핵심 포인트

범주형 피처가 포함된 데이터셋에서 결측값이 발견된 경우, MICE(Multiple Imputations by Chained Equations) 기법을 적용하여 피처 간 관계를 보존하면서 모델 신뢰도를 유지할 수 있다.

## 📝 설명

### MICE가 범주형 데이터 결측값 처리에 적합한 이유

MICE는 반복적 체인 방정식을 통해 결측값을 처리하는 고급 통계 기법으로, 단순한 대체 방법과 달리 피처 간 복잡한 관계를 모델링한다. 각 결측 피처를 다른 피처들의 함수로 모델링하여 여러 개의 완전한 데이터셋을 생성하고, 이를 통해 불확실성을 정량화한다.

### 아키텍처 플로우

```
원본 데이터 (결측값 포함)
    ↓
초기 대체값 설정 (평균/최빈값)
    ↓
반복적 체인 방정식 적용
    ↓ (각 피처별로 순환)
피처 A 모델링 ← 다른 모든 피처들
    ↓
피처 B 모델링 ← 다른 모든 피처들
    ↓
... (수렴까지 반복)
    ↓
다중 완전 데이터셋 생성 (m개)
    ↓
각 데이터셋에서 모델 훈련
    ↓
결과 통합 (풀링)
```

### Trade-offs 고려사항

**MICE 장점**:
- 피처 간 상관관계와 복잡한 패턴 보존
- 결측값 대체의 불확실성을 통계적으로 반영
- 수치형과 범주형 데이터 모두 처리 가능
- MAR(Missing At Random) 가정 하에서 편향 없는 추정
- 다중 대체를 통한 변동성 적절히 반영

**MICE 단점**:
- 계산 복잡도가 높아 처리 시간 증가
- 수렴성 문제 발생 가능
- 모델 복잡성으로 인한 해석 어려움
- 대용량 데이터에서 메모리 사용량 증가

**평균 대체(Mean Imputation) 단점**:
- 범주형 데이터에는 적용 불가능
- 피처 간 상관관계 무시로 편향된 결과 생성
- 분산 축소로 인한 통계적 검정력 감소

**컬럼 삭제(Listwise Deletion) 단점**:
- 정보 손실로 인한 통계적 검정력 약화
- 편향된 표본으로 인한 일반화 성능 저하
- 작은 데이터셋에서 치명적 영향

**0으로 대체 단점**:
- 범주형 데이터에서 의미론적 오류 발생
- 새로운 카테고리 생성으로 인한 모델 혼동
- 실제 분포와 다른 인위적 패턴 생성

## 🔍 주요개념

### 결측값 메커니즘 비교

- **MCAR (Missing Completely At Random)**: 결측이 완전히 무작위로 발생
- **MAR (Missing At Random)**: 관측된 변수들로 결측 패턴 설명 가능
- **MNAR (Missing Not At Random)**: 결측 자체가 정보를 포함

### 실전 적용

- **고객 세분화**: 인구통계학적 범주형 변수 (연령대, 직업, 지역) 결측값 처리
- **상품 추천**: 사용자 선호도 카테고리 (장르, 브랜드, 스타일) 결측 데이터 보완
- **의료 진단**: 증상 및 병력 범주형 변수의 체계적 결측값 대체

## 📝 관련 문제

**Question:** During data preparation, a Machine Learning Specialist has discovered missing values on some columns of a dataset containing categorical features. The Specialist is worried that this might misrepresent the data and reduce model reliability. Which solution should the Specialist implement that will MOST likely produce the best representation of the data?

**Options:**

- A) Apply a mean imputation to replace missing values
- B) Drop the columns with NaN values  
- C) Apply Multiple Imputations by Chained Equations (MICE)
- D) Replace the NaN values with 0

**정답: C) Apply Multiple Imputations by Chained Equations (MICE)**

💡 추가 설명:

- **A) Mean Imputation** - 범주형 데이터에는 평균 개념이 없어 적용 불가능하며, 수치형에서도 피처 간 관계를 무시
- **B) Drop Columns** - 정보 손실이 크고 특히 중요한 예측 변수를 포함할 경우 모델 성능 저하 초래  
- **D) Replace with 0** - 범주형 변수에서 0은 의미 없는 값으로 데이터 왜곡 발생 (예: 사이즈 컬럼에서 small/medium/large 중 0은 무의미)