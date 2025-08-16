---

title: underfitting-model-complexity-parabolic
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/underfitting
- concept/model-complexity
- pattern/parabolic
- diagnosis/linear-output
- bias/high
aliases: ["underfitting", "model-simplicity", "high-bias"]

---

# 언더피팅 - 모델 복잡도 부족으로 인한 파라볼릭 패턴 학습 실패

## 🎯 핵심 포인트

파라볼릭(포물선) 함수를 학습해야 하는 경우 모델이 선형 함수로만 출력된다면, 모델이 언더피팅되어 복잡한 비선형 패턴을 캐치하지 못하는 상태라고 판단할 수 있다.

## 📝 설명

### 언더피팅이 파라볼릭 함수 학습에 적합하지 않은 이유

언더피팅은 모델이 데이터의 기본적인 패턴조차 제대로 학습하지 못하는 상태입니다. 파라볼릭 함수는 2차 함수의 곡선 형태로, 복잡한 비선형 관계를 나타냅니다. 그러나 언더피팅된 모델은:

- **표현력 부족**: 단순한 선형 관계만 학습 가능
- **높은 편향(High Bias)**: 모델 자체의 한계로 인한 체계적 오류
- **패턴 인식 실패**: 데이터의 곡선성을 인식하지 못함
- **일관된 예측 오류**: 훈련 데이터와 테스트 데이터 모두에서 성능 저하

### 아키텍처 플로우

```
실제 데이터 (파라볼릭)
    ↓
단순한 모델 (선형 함수)
    ↓
선형 예측 출력
    ↓
지속적인 예측 오류
    ↓
언더피팅 진단
```

### Trade-offs 고려사항

**언더피팅 모델의 특징**:

- 훈련 데이터에서도 높은 오류율
- 단순한 패턴만 인식 가능
- 빠른 훈련 속도
- 메모리 사용량 적음

**언더피팅 모델의 문제점**:

- 데이터의 복잡한 관계를 놓침
- 예측 성능이 현저히 낮음
- 비즈니스 가치 창출 어려움

**적절한 복잡도 모델의 장점**:

- 비선형 패턴 학습 가능
- 파라볼릭 함수 정확히 근사
- 더 나은 예측 성능

**과복잡 모델의 단점**:

- 오버피팅 위험 (구불구불한 함수)
- 노이즈까지 학습하여 일반화 성능 저하
- 높은 분산(High Variance) 문제

## 🔍 주요개념

### 모델 피팅 상태 비교

- **언더피팅**: 모델이 너무 단순하여 데이터의 기본 패턴도 학습 못함. 높은 편향, 낮은 분산
- **적절한 피팅**: 데이터의 진짜 패턴을 잘 학습하면서 노이즈는 무시. 균형잡힌 편향-분산
- **오버피팅**: 모델이 너무 복잡하여 노이즈까지 학습. 낮은 편향, 높은 분산

### 실전 적용

- **이미지 분류**: 단순한 선형 분류기로 복잡한 이미지 패턴 학습 시도
- **시계열 예측**: 계절성과 트렌드를 가진 데이터를 단순 평균으로 예측
- **고객 행동 분석**: 복잡한 구매 패턴을 단순 선형 회귀로 모델링

## 📝 관련 문제

**Question:** A Machine Learning Specialist is approximating a model that will fit a parabolic target function. The Specialist keeps getting consistent wrong predictions. Additionally, the trained model appears to have a linear function when plotted. What could be the reason behind this?

**Options:**

- A) The model is balanced
- B) The model is underfitting
- C) The model is overfitting  
- D) The model has a high variance
- E) The model complexity is optimal

**정답: B) The model is underfitting**

💡 추가 설명:

- **옵션 A (The model is balanced)** - 균형잡힌 모델은 좋은 성능을 보이므로 지속적인 예측 오류와 모순
- **옵션 C (The model is overfitting)** - 오버피팅된 모델은 구불구불한(squiggly) 형태로 나타나며, 선형 출력과 반대
- **옵션 D (The model has a high variance)** - 높은 분산은 주로 오버피팅과 연관되며, 현재 상황과 맞지 않음
- **옵션 E (The model complexity is optimal)** - 최적 복잡도라면 파라볼릭 함수를 제대로 학습했을 것

**해결책**: 모델 복잡도 증가 (더 많은 파라미터, 고차항 추가, 더 복잡한 알고리즘 사용)