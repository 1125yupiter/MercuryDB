---

title: regression-classification-ml-problemtype
created: 2025-08-28
modified: 2025-08-28
tags:
- problem/regression
- problem/classification
- method/supervised
- algorithm/linear-regression
- algorithm/logistic-regression
aliases: ["regression vs classification", "ml problem types", "supervised learning"]

---

# 회귀와 분류 문제 구분 및 적절한 머신러닝 알고리즘 선택

## 🎯 핵심 포인트

연속적인 수치를 예측해야 하는 경우 회귀 문제에서, 선형 회귀 모델을 사용할 수 있다.

## 📝 설명

### 회귀 문제가 수치 예측에 적합한 이유

회귀 문제는 연속적인 수치값을 예측하는 것이 목표이며, 예측하고자 하는 대상이 구체적인 숫자로 표현될 수 있을 때 사용됩니다. 판매량, 가격, 온도 등과 같이 범위 내에서 무한한 값을 가질 수 있는 변수들을 다룹니다. 선형 회귀는 입력 변수들과 출력 변수 간의 선형 관계를 모델링하여 새로운 데이터에 대한 수치 예측을 수행합니다.

### 아키텍처 플로우

```
입력 데이터 (제품 특성) → 선형 회귀 모델 → 예측 판매량 (연속 수치)
    ↓
제품 A → 15,000개
제품 B → 12,000개  
제품 C → 18,000개
    ↓
최고 예측값 선택 → 제품 C 선택
```

### Trade-offs 고려사항

**회귀 모델 장점**:
- 구체적인 수치 예측 가능
- 예측값 간 크기 비교 가능
- 연속적인 범위에서 정밀한 예측
- 비즈니스 의사결정에 직접적 활용

**회귀 모델 단점**:
- 선형 관계 가정 시 복잡한 패턴 포착 한계
- 이상치에 민감함
- 다중공선성 문제 발생 가능

**분류 모델 장점**:
- 명확한 범주 구분
- 확률 정보 제공
- 의사결정 경계 명확

**분류 모델 단점**:
- 연속적 수치 예측 불가
- 범주 간 세밀한 차이 무시
- 현재 문제(수치 예측)에 부적합

## 🔍 주요개념

### 핵심 개념 비교

- **회귀(Regression)**: 연속적인 수치값을 예측하는 문제. 출력이 실수 범위의 값
- **분류(Classification)**: 미리 정의된 범주 중 하나를 선택하는 문제. 출력이 이산적 클래스
- **선형 회귀**: 입력 변수들의 선형 결합으로 연속 수치를 예측
- **로지스틱 회귀**: 이름과 달리 분류 알고리즘. 확률을 계산하여 범주 분류

### 실전 적용

- **판매량 예측**: 신제품의 월간 판매 개수를 구체적으로 예측
- **부동산 가격 예측**: 집의 특성을 바탕으로 정확한 매매가 산정  
- **재고 수요 예측**: 다음 분기 제품별 필요 재고량 계산

## 📝 관련 문제

**Question:** You work for a manufacturing company that produces retail apparel. Your head of manufacturing has asked you to use your data science skills to determine which product, among a list of potential next products, your company should invest its resources to produce. You decide that you need to predict the sales levels of each of the potential next products and select the one with the highest predicted purchase rate. Which type of machine learning approach should you use?

**Options:**

- A) You are trying to solve for the greatest number of sales across the potential next products. Therefore, you are solving a multiclass classification problem, and you should use multinomial logistic regression.
- B) You are trying to solve for the greatest number of sales across the potential next products. Therefore, you are solving a classification problem, and you should use the random cut forest model.
- C) You are trying to solve for the greatest number of sales across the potential next products. Therefore, you are solving a regression problem, and you should use a linear regression model.
- D) You are trying to solve for the greatest number of sales across the potential next products. Therefore, you are solving a binary classification problem, and you should use a logistic regression model.

**정답: C) 회귀 문제 + 선형 회귀 모델**

💡 추가 설명:

- **옵션 A** - 다항 로지스틱 회귀는 3개 이상의 범주를 분류할 때 사용하며, 판매량이라는 연속 수치 예측에는 부적합
- **옵션 B** - 분류 문제로 잘못 정의했으며, 구체적인 판매량 수치를 예측할 수 없음
- **옵션 D** - 이진 분류는 두 개 범주만 구분하며, 연속적인 판매량 예측이 목적이 아님

---