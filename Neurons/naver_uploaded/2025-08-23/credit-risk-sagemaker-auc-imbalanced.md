---

title: credit-risk-sagemaker-auc-imbalanced
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/classification
- service/sagemaker-autopilot
- metric/auc
- constraint/imbalanced-data
- usecase/credit-risk
aliases: ["credit-risk-model", "auc-imbalanced", "sagemaker-autopilot-auc"]

---

# 불균형 데이터에서 SageMaker AutoPilot을 활용한 신용 리스크 모델링

## 🎯 핵심 포인트

불균형한 신용 데이터(95% 정상, 5% 연체)에서 정상 고객의 대출 거절을 최소화하고자 하는 경우, SageMaker AutoPilot에서 AUC 메트릭과 Binary Classification을 사용할 수 있다.

## 📝 설명

### SageMaker AutoPilot이 신용 리스크 모델링에 적합한 이유

SageMaker AutoPilot은 최소한의 운영 오버헤드로 고품질 ML 모델을 구축할 수 있는 완전 관리형 AutoML 서비스입니다. 특히 신용 리스크 모델링에서는 다음과 같은 이점을 제공합니다:

- **자동 클래스 불균형 처리**: 95:5 비율의 불균형 데이터를 자동으로 감지하고 적절한 처리 기법 적용
- **특성 엔지니어링 자동화**: 신용 관련 변수들의 복잡한 상호작용을 자동으로 발견
- **하이퍼파라미터 최적화**: 다양한 알고리즘과 설정을 자동으로 실험하여 최적 모델 선택
- **모델 해석성**: SHAP 값을 통한 예측 근거 제공으로 규제 요구사항 충족

### 아키텍처 플로우

```
Training Data (S3) → SageMaker AutoPilot → Feature Engineering
       ↓                                           ↓
   10,000 cases                               Automated Processing
   (95% repaid,                                      ↓
    5% default)                              Model Training & Selection
       ↓                                           ↓
   Binary Classification                     AUC Optimization
   Problem Type                                     ↓
       ↓                                    Threshold Optimization
   AUC Evaluation                                  ↓
   Metric                                  Best Model Deployment
```

### Trade-offs 고려사항

**SageMaker AutoPilot 장점**:
- 최소한의 운영 오버헤드로 고품질 모델 구축
- 자동 클래스 불균형 처리 및 특성 엔지니어링
- 다양한 알고리즘 자동 비교 및 최적화
- 모델 해석성과 규제 준수 기능 제공

**SageMaker AutoPilot 단점**:
- 커스터마이징 옵션이 수동 접근법 대비 제한적
- 블랙박스 특성으로 세부 제어 어려움
- 대용량 데이터셋에서는 처리 시간이 길 수 있음

**추가 데이터 수집 + XGBoost 장점**:
- 더 많은 연체 사례로 모델 성능 향상 가능
- XGBoost의 뛰어난 성능과 해석성
- 세밀한 하이퍼파라미터 제어 가능

**추가 데이터 수집 + XGBoost 단점**:
- 높은 운영 오버헤드 (데이터 수집, 라벨링, 검증)
- 추가 비용과 시간 소요
- 데이터 품질 보장의 어려움

## 🔍 주요개념

### 핵심 메트릭 비교

- **Accuracy**: 전체 예측 중 맞춘 비율. 불균형 데이터에서는 다수 클래스에 편향되어 misleading할 수 있음
- **AUC (Area Under the ROC Curve)**: 모든 분류 임계값에서의 성능을 종합한 지표. 클래스 불균형에 상대적으로 강건함

### 실전 적용

- **False Positive 최소화**: 실제 정상 고객을 연체 위험으로 잘못 분류하는 것을 방지하여 수익 기회 손실 최소화
- **적절한 임계값 설정**: AUC를 통해 비즈니스 요구사항에 맞는 최적 분류 임계값 결정
- **규제 준수**: 금융권에서 요구하는 모델 투명성과 해석가능성 확보

## 📝 관련 문제

**Question:** A machine learning specialist working for an investment firm is building a credit risk model to identify potentially risky loans. The training data includes 10,000 cases collected from past customers. Upon inspection, the specialist found that 95% of the cases consisted of fully repaid loans, and only 5% defaulted loans. The company wants to minimize the number of loan rejections from non-defaulters. Which method can the specialist implement to satisfy the requirements with the LEAST operational overhead?

**Options:**

- A) Ask for additional cases focusing on defaulted loans and add them to the training data. Use Amazon SageMaker's built-in XGBoost algorithm with Area Under the ROC Curve (AUC) as the evaluation metric.
- B) Run an Amazon SageMaker AutoPilot job using accuracy as the evaluation metric. Set the problem type to Regression.
- C) Run an Amazon SageMaker AutoPilot job using accuracy as the evaluation metric. Set the problem type to Binary classification.
- D) Run an Amazon SageMaker AutoPilot job using Area Under the ROC Curve (AUC) as the evaluation metric. Set the problem type to Binary classification.

**정답: D) Run an Amazon SageMaker AutoPilot job using Area Under the ROC Curve (AUC) as the evaluation metric. Set the problem type to Binary classification.**

💡 추가 설명:

- **Option A** - 추가 데이터 수집은 높은 운영 오버헤드를 요구하며, 문제에서 요구하는 "최소 운영 오버헤드" 조건에 부합하지 않음
- **Option B** - Regression은 연속값 예측을 위한 것으로, 대출 승인/거절의 이진 분류 문제에 부적절
- **Option C** - Accuracy는 불균형 데이터에서 다수 클래스(정상 대출)에 편향되어 실제 모델 성능을 왜곡할 수 있음. 모든 케이스를 정상으로 예측해도 95% 정확도를 달성할 수 있어 의미 없음