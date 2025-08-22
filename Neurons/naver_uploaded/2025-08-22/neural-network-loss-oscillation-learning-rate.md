---

title: neural-network-loss-oscillation-learning-rate
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/optimization
- technique/gradient-descent
- issue/loss-oscillation
- parameter/learning-rate
- training/neural-network
aliases: ["loss-oscillation", "learning-rate-tuning", "gradient-descent-issues"]

---

# 신경망 손실함수 진동 문제 해결 - 학습률 최적화 기법

## 🎯 핵심 포인트

신경망 훈련 중 손실 함수가 진동하는 경우, 학습률이 너무 높아서 최적값 주변에서 발산하므로 학습률을 낮춰야 한다.

## 📝 설명

### 학습률이 손실 함수 진동에 미치는 영향

학습률(Learning Rate)은 경사 하강법에서 가중치 업데이트의 크기를 결정하는 핵심 하이퍼파라미터입니다. 너무 높은 학습률은 최적값을 찾는 과정에서 목표 지점을 "뛰어넘는" 현상을 일으켜 손실 함수가 위아래로 진동하게 만듭니다.

### 학습률과 수렴 패턴

```
높은 학습률: Loss ↗️ ↘️ ↗️ ↘️ (진동)
적절한 학습률: Loss ↘️ ↘️ ↘️ → (안정적 수렴)  
낮은 학습률: Loss ↘️ ↘️ ↘️ ... (느린 수렴)
```

### Trade-offs 고려사항

**높은 학습률의 영향**:
- 빠른 초기 학습 속도
- 최적값 주변에서 진동 발생
- 극단적인 경우 발산(divergence)

**낮은 학습률의 영향**:
- 안정적인 수렴
- 매우 느린 학습 속도
- 지역 최적값에 갇힐 위험

**적응적 학습률 방법**:
- Adam, RMSprop 등 자동 조정
- Learning Rate Scheduling 적용
- 학습 과정에서 점진적 감소

## 🔍 주요개념

### 학습률 관련 핵심 개념

- **Gradient Descent**: 손실 함수의 기울기 방향으로 가중치를 업데이트하는 최적화 알고리즘
- **Step Size**: 학습률이 결정하는 각 업데이트의 크기
- **Convergence**: 손실 함수가 최적값에 수렴하는 과정
- **Oscillation**: 최적값 주변에서 손실이 위아래로 진동하는 현상

### 실전 적용

- **학습률 감소**: 현재 값의 1/10로 줄여서 테스트
- **적응적 옵티마이저**: Adam (lr=0.001), RMSprop 사용
- **스케줄링**: StepLR, ExponentialLR로 점진적 감소
- **Early Stopping**: 진동 감지 시 자동 중단

## 📝 관련 문제

**Question:** A Machine Learning Specialist splits up a large dataset into batches to train a neural network model. After some iterative tests, the Specialist noticed that the loss function is oscillating. Which is the MOST probable cause of the problem?

**Options:**

- A) The batch sizes are not small enough
- B) The learning rate is too low  
- C) The learning rate is too high
- D) The initial weight in finding the gradient descent is too high

**정답: C) The learning rate is too high**

💡 추가 설명:

- **A) Batch sizes are not small enough** - 작은 배치 크기는 학습을 느리게 할 뿐 진동의 직접적 원인이 아님
- **B) The learning rate is too low** - 낮은 학습률은 느린 수렴을 야기하지만 진동을 일으키지 않음  
- **D) The initial weight is too high** - 초기 가중치는 시작점에만 영향을 주며 지속적인 진동의 원인이 되지 않음