---

title: overfitting-regularization-generalization
created: 2025-08-24
modified: 2025-08-24
tags:
- problem/overfitting
- technique/regularization
- concept/generalization
- method/deep-learning
- evaluation/validation

aliases: ["overfitting", "regularization", "generalization"]

---

# 머신러닝 과적합 문제와 정규화 기법을 통한 일반화 성능 향상

## 🎯 핵심 포인트

훈련 정확도가 검증 정확도보다 현저히 높은 경우 과적합 상황에서, 정규화(Regularization) 기법을 적용하여 일반화 성능을 향상시킬 수 있다.

## 📝 설명

### 과적합 진단과 정규화의 필요성

과적합은 모델이 훈련 데이터에 과도하게 특화되어 새로운 데이터에 대한 성능이 저하되는 현상이다. 훈련 정확도와 검증 정확도 사이의 큰 차이(일반적으로 5% 이상)는 과적합의 강한 신호이며, 이를 해결하기 위해서는 정규화 기법이 필요하다.

**과적합 판단 기준:**
- 훈련 정확도 > 검증 정확도 (상당한 차이)
- 훈련이 진행될수록 검증 정확도가 정체되거나 하락
- 인간 수준 성능에 근접했음에도 일반화 성능이 낮음

### 정규화 기법의 분류와 작동 원리

```
데이터 입력
    ↓
데이터 증강 (Regularization)
    ↓
모델 학습 + L1/L2 정규화 (Regularization)
    ↓
Dropout 적용 (Regularization)
    ↓
Early Stopping 모니터링 (Regularization)
    ↓
일반화된 모델
```

### Trade-offs 고려사항

**정규화 기법 적용 장점:**
- 과적합 방지로 검증/테스트 성능 향상
- 모델의 일반화 능력 증대
- 안정적인 예측 성능 확보

**정규화 기법 적용 단점:**
- 훈련 정확도 일부 희생 가능성
- 하이퍼파라미터 튜닝 복잡도 증가
- 훈련 시간 증가 (일부 기법)

**Epoch 증가의 장점:**
- 언더피팅 상황에서는 성능 향상 가능
- 단순하고 직관적인 접근법

**Epoch 증가의 단점:**
- 과적합 상황에서는 문제 악화
- 훈련-검증 정확도 gap 확대 위험

## 🔍 주요개념

### 정규화 유형 비교

- **Normalization (데이터 정규화)**: Min-Max Scaling, Z-score 등 데이터 전처리 단계에서 스케일을 맞추는 기법
- **Regularization (모델 정규화)**: L1/L2, Dropout, Early Stopping 등 과적합을 방지하여 일반화 성능을 높이는 기법

### 주요 정규화 기법들

- **L1/L2 정규화**: 가중치에 페널티를 추가하여 복잡한 모델 학습 억제
- **Dropout**: 훈련 중 일부 뉴런을 무작위로 비활성화하여 특정 뉴런 의존성 차단
- **Early Stopping**: 검증 손실이 증가하기 시작할 때 훈련을 중단
- **Data Augmentation**: 기존 데이터를 변형하여 훈련 데이터의 다양성 증대
- **Batch Normalization**: 배치별 정규화로 학습 안정성과 일반화 성능 동시 향상

### 실전 적용

- **이미지 분류**: Dropout + Data Augmentation + Early Stopping 조합으로 CNN 모델 정규화
- **자연어 처리**: L2 정규화 + Dropout으로 과적합이 쉬운 RNN/Transformer 모델 개선  
- **제조업 결함 분류**: 본 사례처럼 충분한 데이터에서도 발생하는 과적합을 정규화로 해결

## 📝 관련 문제

**Question:** A manufacturing business requests assistance from a machine learning expert in developing a model that classifies damaged components into one of eight defect classes. For training purposes, the business gave over 100,000 photos per fault category. The expert observes that the validation accuracy of the picture classification model is 80%, whereas the training accuracy is 90%. Human-level performance for this sort of picture categorization is estimated to be approximately 90%. What should the professional consider while resolving this situation?

**Options:**
- A) A longer training time
- B) Making the network larger  
- C) Using a different optimizer
- D) Using some form of regularization
- E) Collecting more training data

**정답: D) Using some form of regularization**

💡 추가 설명:

- **A) A longer training time** - 과적합 상황에서 훈련을 더 오래 하면 훈련-검증 정확도 gap이 더 커질 위험이 있음
- **B) Making the network larger** - 모델 용량을 늘리면 과적합이 더 심해질 수 있으며, 현재 문제 해결에 부적절함
- **C) Using a different optimizer** - 옵티마이저 변경은 과적합 문제의 근본적 해결책이 아니며, 일반화 성능 향상에는 제한적임  
- **E) Collecting more training data** - 이미 각 클래스당 10만 장의 충분한 데이터가 있어 데이터 부족이 문제가 아님

**핵심 개념:**
- Regularization은 Generalization을 위한 구체적 방법론들의 집합
- 과적합 진단: 훈련 정확도 ≫ 검증 정확도
- 문제 해석: 구체적 epoch 정보 없이 정확도 gap만 제시된 경우 과적합 해결 접근이 적절