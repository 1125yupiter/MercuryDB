---

title: deeplearning-regularization-overfitting
created: 2025-08-22
modified: 2025-08-22
tags:
- problem/overfitting
- technique/regularization
- method/deep-learning
- service/sagemaker
- solution/l1-l2-dropout
aliases: ["dl-regularization", "overfitting-solution", "reg-techniques"]

---

# 딥러닝 모델의 오버피팅 해결을 위한 정규화 기법

## 🎯 핵심 포인트

딥러닝 모델에서 훈련 정확도와 테스트 정확도 간 큰 격차(20% 이상)가 발생한 경우, L1/L2 정규화와 드롭아웃을 조합하여 적용하면 모델의 일반화 성능을 효과적으로 개선할 수 있다.

## 📝 설명

### 오버피팅 문제 진단과 해결

오버피팅은 모델이 훈련 데이터에 과도하게 맞춰져 새로운 데이터에 대한 일반화 성능이 떨어지는 현상입니다. 주요 증상으로는 훈련 정확도는 높지만(90%) 테스트 정확도는 상대적으로 낮은(70%) 현상이 나타납니다.

### 정규화 기법의 작동 원리

**L1/L2 정규화**:
- 손실함수에 가중치 페널티 항을 추가
- L1: 가중치의 절댓값 합 (sparsity 유도)
- L2: 가중치의 제곱 합 (weight decay)

**드롭아웃**:
- 훈련 중 임의로 뉴런을 비활성화
- 뉴런 간 과도한 의존성 방지
- 모델 앙상블 효과 제공

### 아키텍처 플로우

```
Input Data
    ↓
Dense Layer + L2 Regularization
    ↓
Dropout Layer (p=0.2-0.5)
    ↓
Dense Layer + L1/L2 Regularization
    ↓
Dropout Layer
    ↓
Output Layer
    ↓
Loss = Original_Loss + λ₁∑|w| + λ₂∑w²
```

### Trade-offs 고려사항

**L1/L2 + 드롭아웃 조합 장점**:
- 기존 네트워크 구조 유지하면서 오버피팅 해결
- 점진적 조정 가능 (정규화 강도, 드롭아웃 비율)
- 상호 보완적 효과로 강력한 정규화
- Amazon SageMaker에서 쉽게 구현 가능

**L1/L2 + 드롭아웃 조합 단점**:
- 하이퍼파라미터 튜닝 복잡도 증가
- 훈련 시간 다소 증가
- 과도한 정규화 시 언더피팅 위험

**네트워크 구조 축소 장점**:
- 근본적인 모델 복잡도 감소
- 계산 비용 절약

**네트워크 구조 축소 단점**:
- 모델 표현력 자체 저하 위험
- 현재 구조에 대한 정보 없이 성급한 판단
- 복구 어려움

## 🔍 주요개념

### 정규화 기법 비교

- **L1 정규화**: 가중치를 0으로 만들어 특성 선택 효과, 스파스한 모델 생성
- **L2 정규화**: 가중치를 0에 가깝게 만들되 완전히 제거하지 않음, 안정적인 학습
- **드롭아웃**: 뉴런 레벨에서 임시적 제거, 로버스트한 특성 학습 유도
- **배치 정규화**: 레이어 입력 정규화, 내부 공변량 변화 해결

### 실전 적용

- **전자상거래 추천시스템**: 사용자 행동 예측 모델의 오버피팅 방지
- **의료 영상 진단**: CNN 모델의 일반화 성능 개선
- **금융 신용평가**: 고차원 특성 데이터에서의 안정적인 예측 모델 구축

## 📝 관련 문제

**Question:** A web-based business wishes to increase conversions on its landing page. The business developed a multi-class deep learning network algorithm using Amazon SageMaker with a large historical dataset of client visits. However, there is an overfitting issue: training data indicates a prediction accuracy of 90%, whereas test data indicates only 70%. The organization needs to increase the generalizability of its model before putting it in production to optimize visit-to-purchase conversions. Which activity is recommended to ensure the HIGHEST degree of accuracy on test and validation data?

**Options:**

- A) Increase the randomization of training data in the mini-batches used in training
- B) Allocate a higher proportion of the overall data to the training dataset  
- C) Apply L1 or L2 regularization and dropouts to the training
- D) Reduce the number of layers and units (or neurons) from the deep learning network

**정답: C) Apply L1 or L2 regularization and dropouts to the training**

💡 추가 설명:

- **A) 미니배치 랜덤화 증가** - 훈련 안정성에는 도움이 되지만 오버피팅 직접 해결 효과는 제한적
- **B) 훈련 데이터 비율 증가** - 오히려 오버피팅을 악화시킬 수 있으며, 검증/테스트 데이터 감소로 모델 평가 신뢰성 저하
- **D) 네트워크 구조 단순화** - 더 급진적인 접근으로, 현재 모델 구조를 모르는 상태에서는 성급한 판단이며 모델 성능 자체가 크게 저하될 위험
- **정규화와 드롭아웃 조합**이 첫 번째 권장사항인 이유는 기존 구조를 유지하면서 점진적 조정이 가능하고, 두 기법의 상호 보완적 효과로 강력한 오버피팅 방지가 가능하기 때문