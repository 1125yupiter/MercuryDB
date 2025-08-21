---

title: regression-linear-sales-prediction
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/regression
- algorithm/linear-regression
- usecase/sales-prediction
- method/supervised
- domain/business-forecasting
aliases: ["sales-forecast", "product-demand", "linear-reg"]

---

# 제품 판매량 예측을 위한 선형 회귀 적용

## 🎯 핵심 포인트

제품의 과거 판매 데이터가 있는 경우 선형 회귀에서, 각 제품의 미래 판매량(수치)을 예측할 수 있다.

## 📝 설명

### 선형 회귀가 판매량 예측에 적합한 이유

선형 회귀는 연속적인 수치 값을 예측하는 회귀 문제에 최적화된 알고리즘입니다. 제품 판매량 예측의 경우 "몇 개가 팔릴 것인가?"라는 구체적인 숫자를 구해야 하므로, 분류나 차원축소가 아닌 회귀 방법론이 필요합니다.

선형 회귀는 과거 판매 데이터의 패턴을 학습하여 독립변수(시간, 계절, 마케팅 예산, 가격 등)와 종속변수(판매량) 간의 선형 관계를 모델링합니다. AWS Machine Learning 서비스에서는 산업 표준인 선형 회귀 알고리즘을 기본 회귀 모델로 제공합니다.

### 아키텍처 플로우

```
과거 판매 데이터 → 데이터 전처리 → 선형 회귀 모델 → 훈련 → 예측 모델
     ↓             ↓            ↓           ↓           ↓
[시계열 데이터]    [특성 추출]     [선형 관계]   [가중치 학습] [판매량 예측]
   - 날짜          - 계절성        - y=ax+b      - MSE 최소화  - 구체적 수치
   - 제품ID        - 트렌드        - 다중 변수     - 경사하강법   - 신뢰구간
   - 판매량        - 외부 요인      - 연속 출력                 - 비즈니스 활용
```

### Trade-offs 고려사항

**Linear Regression 장점**:
- 연속적 수치 값 예측에 최적화
- 해석 가능성이 높음 (계수로 영향도 파악)
- 계산 효율성 우수
- 과적합 위험 낮음
- AWS ML에서 표준 제공

**Linear Regression 단점**:
- 선형 관계만 포착 가능
- 이상치에 민감
- 복잡한 비선형 패턴 처리 한계

**Logistic Regression 장점**:
- 분류 문제에 강력함
- 확률 출력으로 해석 용이

**Logistic Regression 단점**:
- **이진 분류용 (0/1)으로 판매량 수치 예측 불가**
- 연속값이 아닌 범주형 출력

**PCA 장점**:
- 차원 축소로 노이즈 제거
- 계산 복잡도 감소

**PCA 단점**:
- **예측 모델이 아닌 전처리 기법**
- 단독으로 판매량 예측 불가능

**Factorization Machine (Binary Classifier) 장점**:
- 고차원 희소 데이터 처리 우수
- 상호작용 효과 포착

**Factorization Machine (Binary Classifier) 단점**:
- **Binary 설정으로 0/1만 출력, 판매량 수치 예측 불가**
- 회귀 문제에 부적합한 설정

## 🔍 주요개념

### 문제 유형 비교

- **회귀 문제**: 연속적 수치 값 예측 (판매량, 가격, 온도)
- **분류 문제**: 범주 예측 (스팸/정상, 승인/거부, A/B/C 등급)
- **클러스터링**: 그룹화 (고객 세분화, 제품 카테고리)

### 실전 적용

- **소매업체**: 계절별 제품 재고 최적화를 위한 월별 판매량 예측
- **제조업**: 원자재 조달 계획 수립을 위한 분기별 수요 예측  
- **전자상거래**: 프로모션 효과 측정을 위한 캠페인별 판매량 예측

## 📝 관련 문제

**Question:** A company has a repository of historical records of its product sales. The company wants to use this data to discover valuable insights using machine learning. Their main objective is to predict how many units will sell for each particular product. Which machine learning method should be used to accomplish this task?

**Options:**

- A) Logistic Regression
- B) Principal Component Analysis (PCA)  
- C) Factorization Machines with a binary_classifier predictor type
- D) Linear Regression
- E) K-means Clustering

**정답: D) Linear Regression**

💡 추가 설명:

- **Logistic Regression (A)** - 이진 분류용으로 0 또는 1만 출력하여 판매량 수치 예측 불가
- **PCA (B)** - 차원 축소 전처리 기법으로 예측 모델이 아님
- **Factorization Machines with binary_classifier (C)** - Binary 설정으로 수치값이 아닌 이진 결과만 출력
- **K-means Clustering (E)** - 비지도 학습으로 레이블 예측이 아닌 그룹화 수행