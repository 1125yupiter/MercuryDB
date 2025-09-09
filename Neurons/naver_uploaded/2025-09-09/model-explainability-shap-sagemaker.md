---

title: model-explainability-shap-sagemaker
created: 2025-08-26
modified: 2025-08-26
tags:
- service/sagemaker
- technique/shap
- constraint/interpretability
- usecase/credit-scoring
- compliance/regulatory
aliases: ["model-interpretability", "SHAP", "explainability"]

---

# 모델 해석 가능성: SHAP와 SageMaker Model Monitor를 활용한 신용 결정 설명

## 🎯 핵심 포인트

개별 고객의 신용 결정에 대한 구체적인 설명이 필요한 경우 SageMaker Model Monitor에서, SHAP 값을 활용하여 각 피처가 특정 예측에 미친 영향을 분석할 수 있다.

## 📝 설명

### SHAP가 신용 결정 설명에 적합한 이유

SHAP(SHapley Additive exPlanations)는 게임 이론의 Shapley 값을 기반으로 개별 예측에 대한 각 피처의 기여도를 정확히 계산합니다. 신용 심사와 같이 규제 요구사항으로 인해 개별 결정에 대한 명확한 설명이 필요한 경우, 전역적 피처 중요도와 달리 "왜 이 특정 고객이 거부되었는가?"에 대한 구체적인 답변을 제공할 수 있습니다.

SageMaker Model Monitor는 배포된 모델의 실시간 추론 결과를 수집하고 저장하여, 이후 SHAP 분석의 원재료를 제공합니다. 이를 통해 지속적인 모델 해석과 규제 준수를 동시에 달성할 수 있습니다.

### 아키텍처 플로우

```
Historical Data → XGBoost Training (SageMaker) → Model Deployment (Endpoint)
                                                      ↓
Customer Request → Real-time Inference → Model Monitor (Data Collection)
                                                      ↓
Inference Data → SHAP Analysis → Feature Contribution Chart → Credit Team
```

### Trade-offs 고려사항

**SHAP 장점**:
- 개별 예측에 대한 정확한 설명 제공
- 규제 요구사항 만족 (Fair Credit Reporting Act 등)
- 게임 이론 기반의 수학적으로 엄밀한 방법론
- 피처별 기여도의 합이 예측값과 기준값의 차이와 일치

**SHAP 단점**:
- 계산 비용이 높음 (특히 복잡한 모델)
- 실시간 설명 생성 시 레이턴시 증가 가능
- 비전문가에게는 해석이 여전히 복잡할 수 있음

**Feature Importance 장점**:
- 빠른 계산 속도
- 전체 모델에 대한 직관적 이해
- 구현이 간단함

**Feature Importance 단점**:
- 개별 예측에 대한 설명 불가
- "왜 이 고객이 거부되었는가?"에 대한 답변 제공 불가
- 규제 요구사항 미충족

## 🔍 주요개념

### 해석 가능성 방법론 비교

- **SHAP (Local Explainability)**: 개별 예측의 각 피처별 기여도를 정확히 계산. "김철수 고객의 경우 나이(+0.3), 소득(-0.7), 신용점수(+0.5)가 최종 결정에 영향"
- **Feature Importance (Global Explainability)**: 모델 전체에서 각 피처의 일반적 중요도. "전체적으로 소득이 가장 중요한 피처이고, 나이, 신용점수 순"

### 실전 적용

- **신용카드 발급 거부 시**: SHAP로 구체적 거부 사유를 고객에게 설명
- **대출 심사 투명성**: 각 고객별 맞춤형 리스크 요인 분석 제공
- **규제 감사 대응**: 개별 결정에 대한 객관적이고 수학적인 근거 제시

## 📝 관련 문제

**Question:** A bank wants to introduce a low-interest credit campaign targeting specific customers affected by economic difficulties. The credit team uses an XGBoost model with sufficient accuracy but struggles to explain why certain customers are rejected for credit. The team has little data science expertise and needs to understand individual credit decisions for regulatory compliance. What should the data science team implement to address this explainability challenge most effectively?

**Options:**

- A) Use Amazon SageMaker Studio with XGBoost training container, deploy model at endpoint, enable SageMaker Model Monitor to store inferences, and create SHAP values to generate feature contribution charts
- B) Use Amazon SageMaker Studio with XGBoost training container, activate SageMaker Debugger to calculate Shapley values during training
- C) Create SageMaker notebook instance, retrain model locally with XGBoost library, use plot_importance() method to create feature importance charts
- D) Use SageMaker Studio with XGBoost training container, deploy model, use SageMaker Processing to automatically create feature importance explainability charts

**정답: A) SageMaker Model Monitor + SHAP**

💡 추가 설명:

- **Option B (SageMaker Debugger)** - 훈련 중 디버깅 도구로, 배포 후 개별 추론 설명과는 무관함
- **Option C (plot_importance())** - 전역적 피처 중요도만 제공하여 개별 고객별 결정 설명 불가
- **Option D (SageMaker Processing)** - Feature importance 기반으로 "왜 이 특정 고객이 거부되었나?"에 답변 불가

---