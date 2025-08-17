---

title: model-drift-sagemaker-retraining
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/model-drift
- service/sagemaker
- technique/retraining
- constraint/performance-degradation
- usecase/product-recommendation
aliases: ["model-drift", "concept-drift", "retraining"]

---

# SageMaker 모델 드리프트와 재학습 전략

## 🎯 핵심 포인트

모델 성능이 시간이 지남에 따라 저하되는 모델 드리프트가 발생한 경우, 원본 데이터와 새로운 데이터를 결합하여 주기적으로 재학습을 수행할 수 있다.

## 📝 설명

### SageMaker가 모델 드리프트 대응에 적합한 이유

Amazon SageMaker는 모델 라이프사이클 관리를 위한 완전 관리형 플랫폼으로, 모델 드리프트 감지와 자동 재학습 파이프라인 구축에 최적화되어 있습니다. SageMaker Model Monitor를 통해 실시간으로 데이터 품질과 모델 성능을 모니터링하고, 임계값 기반 알람을 설정하여 자동 재학습을 트리거할 수 있습니다.

### 아키텍처 플로우

```
고객 행동 데이터 → S3 저장소 → SageMaker Model Monitor
                              ↓
모델 성능 임계값 감지 → CloudWatch 알람 → Lambda 함수
                                        ↓
원본 + 신규 데이터 결합 → SageMaker Training Job → 새 모델 배포
                                                ↓
A/B 테스트 → 성능 검증 → 프로덕션 모델 교체
```

### Trade-offs 고려사항

**주기적 재학습 장점**:
- 최신 고객 행동 패턴 반영
- 예측 정확도 지속적 유지
- 비즈니스 ROI 보장

**주기적 재학습 단점**:
- 학습 비용 증가
- 데이터 저장 비용 누적
- 모델 버전 관리 복잡성

**하이퍼파라미터 튜닝만 수행 장점**:
- 빠른 적용
- 비용 효율성

**하이퍼파라미터 튜닝만 수행 단점**:
- 근본적 데이터 분포 변화 미해결
- 일시적 성능 개선에 그침

## 🔍 주요개념

### 모델 드리프트 유형 비교

- **Data Drift**: 입력 데이터의 분포가 변화하는 현상 (예: 고객 연령대 변화)
- **Concept Drift**: 입력과 출력 간의 관계가 변화하는 현상 (예: 계절별 구매 패턴 변화)

### 실전 적용

- 전자상거래 추천 시스템에서 계절별 상품 선호도 변화 대응
- 금융 사기 탐지 모델에서 새로운 사기 패턴 학습
- 의료 진단 모델에서 새로운 질병 변종 데이터 반영

## 📝 관련 문제

**Question:** A company deployed a machine learning model in Amazon SageMaker that provides product recommendations over a year ago. The model has contributed to increased sales in the first quarter. However, the company has noticed that the number of customers purchasing the recommended products has been steadily declining in recent months. Which approach can be taken to improve the model's effectiveness?

**Options:**

- A) Retrain the model periodically using the original data and apply regularization to improve prediction accuracy
- B) Periodically tune the hyperparameters to reduce model drift
- C) Train a model from scratch based on the new data using a different machine learning algorithm to handle the model drift
- D) Retrain the model periodically using the original and the new data whenever a significant change in customer data is detected

**정답: D) Retrain the model periodically using the original and the new data whenever a significant change in customer data is detected**

💡 추가 설명:

- **옵션 A** - 원본 데이터만으로는 변화된 고객 행동 패턴을 반영할 수 없어 근본적 해결책이 되지 못함
- **옵션 B** - 하이퍼파라미터 튜닝은 데이터 분포 변화에 대한 근본적 해결책이 아니며 일시적 개선에 그침
- **옵션 C** - 기존 알고리즘이 효과적임이 증명된 상황에서 완전히 새로운 모델을 구축하는 것은 비효율적이며 불필요한 복잡성 증가