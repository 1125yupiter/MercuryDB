---

title: employee-churn-classification-sagemaker
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/classification
- service/sagemaker
- usecase/employee-retention
- constraint/binary-outcome
- method/supervised
aliases: ["employee-retention", "churn-prediction", "binary-classification"]

---

# Employee Churn Classification - ML Model Selection

## 🎯 핵심 포인트

직원이 향후 6개월 내 이직할지 잔류할지 예측하는 경우 레이블된 데이터가 있다면, Binary Classification 모델을 사용할 수 있다.

## 📝 설명

### Classification이 Employee Churn 예측에 적합한 이유

Employee Churn 예측은 전형적인 **Binary Classification** 문제입니다. 직원이 미래에 취할 행동을 두 개의 범주("Stay" 또는 "Leave")로 분류하는 것이 목적이며, 과거 직원 데이터에서 실제 이직 여부가 레이블로 제공되어 지도학습이 가능합니다.

Amazon SageMaker에서는 Binary Classification을 위해 **Logistic Regression** 알고리즘을 기본으로 사용하며, XGBoost, Random Forest 등 다양한 알고리즘도 지원합니다. 모델은 직원의 특성(근무연수, 급여, 성과평가, 부서 등)을 입력받아 이직 확률을 계산하고 최종적으로 Stay/Leave를 예측합니다.

### 아키텍처 플로우

```
Historical Employee Data (S3)
↓
Data Preprocessing (SageMaker Processing)
↓
Feature Engineering (근무연수, 급여, 성과평가, 부서 등)
↓
Model Training (Binary Classification - Logistic Regression/XGBoost)
↓
Model Validation (정확도, 정밀도, 재현율 평가)
↓
Model Deployment (SageMaker Endpoint)
↓
Real-time Prediction (현재 직원 → Stay/Leave 예측)
```

### Trade-offs 고려사항

**Classification 장점**:
- 명확한 이진 결과 제공 (Stay/Leave)
- 비즈니스 의사결정에 직접 활용 가능
- 모델 해석이 용이하고 설명 가능
- 레이블된 데이터로 성능 검증 가능

**Classification 단점**:
- 이직 시점이나 확률적 뉘앙스 제공 어려움
- 데이터 불균형 문제 발생 가능 (일반적으로 잔류 > 이직)
- 정성적 요인(업무 만족도, 개인 사정) 반영 한계

**Regression 장점**:
- 이직 확률을 연속값으로 제공
- 임계값 조정으로 유연한 의사결정 가능

**Regression 단점**:
- 출력값이 확률이 아닌 숫자값이므로 해석 복잡
- 비즈니스 요구사항(Stay/Leave)과 직접 매칭 어려움

## 🔍 주요개념

### ML 모델 타입 비교

- **Binary Classification**: 두 개 클래스 중 하나를 예측 (Stay/Leave, Spam/Not Spam)
- **Multiclass Classification**: 세 개 이상 클래스 예측 (부서 이동 예측: 마케팅/영업/개발/퇴사)
- **Regression**: 연속적 숫자값 예측 (예상 근무 개월 수, 이직 확률 점수)
- **Anomaly Detection**: 정상 패턴에서 벗어난 이상 직원 탐지

### 실전 적용

- **HR 부서**: 이직 위험 직원 사전 식별로 retention 전략 수립
- **경영진**: 핵심 인재 유지를 위한 예산 배분 및 정책 결정
- **팀 매니저**: 팀원 이직 가능성 모니터링으로 업무 연속성 확보

## 📝 관련 문제

**Question:** A company is planning an employee retention strategy to reduce replacement costs. The company hires a Machine Learning Specialist to develop an ML-based solution to tell whether a current employee will leave or stay for the next 6 months. The ML Specialist was given full access to labeled datasets. How should the specialist accomplish this task?

**Options:**

- A) Use an Anomaly detection model type
- B) Use a Reinforcement Learning model type  
- C) Use a Classification model type
- D) Use a Linear Regression model type

**정답: C) Use a Classification model type**

💡 추가 설명:

- **A) Anomaly Detection** - 정상 패턴에서 벗어난 이상 행동 탐지용으로, Stay/Leave 분류에는 부적합
- **B) Reinforcement Learning** - 환경과의 시행착오 학습이 필요한 문제용으로, 레이블된 데이터가 있는 상황에는 부적합  
- **D) Linear Regression** - 연속적 숫자값 예측용으로, 범주형 결과가 필요한 경우 직접적이지 않음