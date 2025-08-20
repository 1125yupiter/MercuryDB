---

title: transfer-multitask-learning-deeplearning
created: 2025-08-20
modified: 2025-08-20
tags:
- method/transfer-learning
- method/multi-task-learning
- technique/fine-tuning
- architecture/shared-backbone
- training/simultaneous
aliases: ["transfer learning", "multi-task learning", "MTL", "TL"]

---

# Transfer Learning vs Multi-Task Learning - 딥러닝 학습 전략 비교

## 🎯 핵심 포인트

관련된 여러 작업을 동시에 수행해야 하는 경우 멀티태스크 러닝에서, 데이터 효율성과 계산 자원 절약을 달성할 수 있다.

## 📝 설명

### Transfer Learning과 Multi-Task Learning이 다른 학습 전략에 적합한 이유

**Transfer Learning**은 대용량 데이터셋에서 사전 훈련된 모델을 새로운 작업에 적응시키는 순차적 학습 방식입니다. 기존 모델의 가중치를 동결하고 마지막 레이어만 새로운 작업에 맞게 미세 조정합니다.

**Multi-Task Learning**은 여러 관련 작업을 동시에 학습하여 공통 특징을 공유하는 병렬 학습 방식입니다. 하나의 모델이 훈련 과정에서 여러 손실 함수를 동시에 최적화합니다.

### 아키텍처 플로우

```
Transfer Learning:
Pre-trained Model → Freeze Layers → Add New Layers → Fine-tune → Target Task

Multi-Task Learning:
Input → Shared Backbone → Task A Head → Loss A
                      → Task B Head → Loss B  
                      → Task C Head → Loss C
                      ↓
              Combined Loss (α×Loss A + β×Loss B + γ×Loss C)
                      ↓
              Backpropagation to Entire Network
```

### Trade-offs 고려사항

**Transfer Learning 장점**:
- 적은 데이터로도 높은 성능 달성 가능
- 사전 훈련된 모델의 풍부한 특징 활용
- 훈련 시간 단축
- 단일 작업에 집중 가능

**Transfer Learning 단점**:
- 도메인 차이가 클 때 성능 저하
- 사전 훈련 모델에 의존적
- 순차적 학습으로 인한 시간 소요

**Multi-Task Learning 장점**:
- 계산 자원 효율성 (하나의 모델로 여러 작업)
- 작업 간 상호 학습으로 일반화 성능 향상
- 데이터 부족 작업의 성능 개선
- 과적합 방지 효과

**Multi-Task Learning 단점**:
- 작업 간 충돌 가능성
- 손실 함수 가중치 설정의 어려움
- 모든 작업에 대한 데이터 필요
- 복잡한 아키텍처 설계

## 🔍 주요개념

### 핵심 개념 비교

- **Transfer Learning**: 사전 훈련된 지식을 새로운 도메인으로 전이하는 순차적 학습 방식
- **Multi-Task Learning**: 관련 작업들을 동시에 학습하여 공통 표현을 학습하는 병렬 학습 방식

### 실전 적용

- **자율주행**: 객체 감지 + 차선 인식 + 신호등 분류를 하나의 모델로 동시 처리
- **의료 영상**: 종양 검출 + 크기 측정 + 악성도 분류를 통합 진단 시스템
- **NLP**: 감정 분석 + 개체명 인식 + 문법 오류 검출을 통합 텍스트 처리

## 📝 관련 문제

**Question:** A company needs to build a computer vision system that simultaneously performs object detection, semantic segmentation, and depth estimation on autonomous vehicle camera feeds. They have limited computational resources and need real-time performance. Which approach would be most effective?

**Options:**

- A) Train three separate transfer learning models, each fine-tuned from ImageNet pre-trained networks
- B) Use multi-task learning with a shared backbone and three task-specific heads
- C) Apply transfer learning sequentially, training each task after the previous one completes
- D) Use ensemble methods combining multiple pre-trained models
- E) Train from scratch for each task independently

**정답: B) Multi-task learning with shared backbone and task-specific heads**

💡 추가 설명:

- **Option A** - 세 개의 별도 모델은 계산 자원과 메모리를 3배로 사용하여 실시간 처리에 부적합
- **Option C** - 순차 학습은 작업 간 상호 학습 효과를 놓치고 시간이 오래 걸림
- **Option D** - 앙상블은 더 많은 계산 자원을 필요로 하여 실시간 제약에 맞지 않음
- **Option E** - 처음부터 훈련은 대량의 데이터와 시간이 필요하여 비효율적

---