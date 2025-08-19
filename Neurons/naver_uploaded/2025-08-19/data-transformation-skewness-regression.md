---
title: data-transformation-skewness-regression
created: 2025-08-16
modified: 2025-08-16
tags:
- technique/data-transformation
- problem/regression
- method/supervised
- constraint/statistical-assumption
- data/skewness
aliases: ["Data-Transformation", "Skewness", "Log-Transform"]
---

# 데이터 변환을 통한 선형 회귀 모델의 통계적 가정 충족

## 🎯 핵심 포인트

데이터의 분포가 **왜도(skewness)**를 보일 경우, **선형 회귀** 모델의 통계적 가정을 충족시키기 위해 **로그 또는 제곱 변환**을 적용하여 데이터 분포를 **정규 분포**에 가깝게 만들 수 있다.

## 📝 설명

### 데이터 변환이 선형 회귀에 적합한 이유

선형 회귀 모델은 예측 변수(predictor)가 정규 분포에 가깝게 분포되어 있을 때 가장 효율적으로 작동하며, 이는 모델의 잔차(residual)가 정규성을 띠도록 하는 데 필수적입니다. 데이터가 한쪽으로 치우쳐 왜곡된 분포(skewed distribution)를 보일 경우, 모델의 예측 성능이 저하될 수 있으며, 통계적 유의성(statistical significance)을 판단하는 데 사용되는 p-value와 같은 지표들이 신뢰성을 잃을 수 있습니다.

데이터 변환은 이러한 왜도를 제거하거나 완화하여 데이터의 분포를 정규 분포와 유사하게 만듭니다. 이를 통해 모델의 기본 가정을 충족시키고, 모델의 예측력과 신뢰성을 높일 수 있습니다.

### 아키텍처 플로우

```
원시 데이터 획득
↓
데이터 시각화 및 분포 확인 (히스토그램, 밀도 플롯)
↓
양의 왜도 (오른쪽 꼬리) 또는 음의 왜도 (왼쪽 꼬리) 판단
↓
적절한 데이터 변환 적용 (로그, 제곱근, 제곱 등)
↓
변환된 데이터로 모델 학습
↓
모델 성능 평가 및 예측
```

### Trade-offs 고려사항

**데이터 변환의 장점**:

- 모델의 통계적 가정(정규성) 충족
    
- 모델의 예측 성능 향상
    
- 이상치(outlier)의 영향력 감소
    
- 잔차의 정규성 확보로 통계적 유의성 분석(p-value) 신뢰도 증가
    

**데이터 변환의 단점**:

- 변환된 데이터를 해석하기 어려워짐
    
- 모델 결과가 직관적이지 않을 수 있음
    
- 과도한 변환은 데이터의 실제 의미를 왜곡할 수 있음
    
- 최적의 변환 방법을 찾기 위한 추가적인 분석 필요
    

## 🔍 주요 개념

### 핵심 개념 비교

- **양의 왜도 (Positive Skewness)**: 데이터의 대부분이 왼쪽에 몰려있고, 오른쪽으로 긴 꼬리를 갖는 분포. 주로 소득, 인구수 등 0보다 큰 값에 제한이 있는 데이터에서 나타남. **로그 변환(log(x))** 또는 **제곱근 변환(sqrtx)**이 효과적이다.
    
- **음의 왜도 (Negative Skewness)**: 데이터의 대부분이 오른쪽에 몰려있고, 왼쪽으로 긴 꼬리를 갖는 분포. 주로 시험 점수, 제품의 수명 등 상한선이 정해진 데이터에서 나타남. **제곱 변환(x2)**이 효과적일 수 있다.
    

### 실전 적용

- **머신러닝 전문가의 월급 데이터 예측**: 평균 월급 데이터는 소수의 고액 연봉자가 존재하여 **양의 왜도**를 보일 가능성이 높음. 이 경우 로그 변환을 적용하여 데이터를 정규화하고 선형 회귀 모델의 예측 정확도를 높일 수 있다.
    
- **주택 가격 예측**: 대부분의 주택 가격은 특정 범위에 밀집되어 있고, 초고가 주택이 **양의 왜도**를 형성할 수 있음.
    
- **고객 구매 금액 예측**: 소수의 VIP 고객들이 구매 금액의 대부분을 차지하여 데이터가 **양의 왜도**를 가지므로 로그 변환이 필수적이다.
    

## 📝 관련 문제

**Question:** A Machine Learning Specialist is developing a linear regression model to predict the average salary of ML-engineers. A quick visualization of the salary data shows a positively skewed distribution. Which transformation should the Specialist apply to the data to meet the model's statistical assumption?

**Options:**

- A) Exponential transformation
    
- B) Laplace transform
    
- C) Logarithmic transformation
    
- D) Third-order polynomial transformation
    

**정답: C) Logarithmic transformation**

💡 추가 설명:

- **A) Exponential transformation**: 데이터의 값을 급격히 증가시켜 왜도를 더욱 심화시키므로 부적절하다.
    
- **B) Laplace transform**: 미분 방정식을 단순화하는 수학적 기법으로, 데이터 분포 정규화 목적과는 무관하다.
    
- **D) Third-order polynomial transformation**: 음의 왜도(왼쪽 꼬리) 데이터를 해결하는 데 더 효과적일 수 있어 양의 왜도에는 적합하지 않다.