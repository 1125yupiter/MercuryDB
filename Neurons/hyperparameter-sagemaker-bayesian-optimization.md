---

title: hyperparameter-sagemaker-bayesian-optimization
created: 2025-08-20
modified: 2025-08-20
tags:
- service/sagemaker
- technique/hyperparameter-tuning
- method/bayesian-optimization
- optimization/automatic
- tuning/intelligent
aliases: ["HPO", "hyperparameter-optimization", "bayesian-tuning"]

---

# Amazon SageMaker 하이퍼파라미터 튜닝 - 베이지안 최적화 기반 자동 튜닝

## 🎯 핵심 포인트

머신러닝 모델의 성능을 최적화해야 하는 경우 Amazon SageMaker에서, 베이지안 최적화 기반의 자동 하이퍼파라미터 튜닝을 통해 효율적으로 최적의 파라미터 조합을 찾을 수 있다.

## 📝 설명

### Amazon SageMaker가 하이퍼파라미터 튜닝에 적합한 이유

Amazon SageMaker는 베이지안 최적화(Bayesian Optimization)를 핵심 엔진으로 사용하여 자동 하이퍼파라미터 튜닝을 제공합니다. 이 방식은 이전 실험 결과를 학습하여 다음에 시도할 최적의 하이퍼파라미터 조합을 지능적으로 예측하므로, 무작위 검색이나 그리드 검색보다 훨씬 효율적입니다. 내장 알고리즘뿐만 아니라 사용자 정의 알고리즘에서도 동일하게 적용할 수 있어 범용성이 뛰어납니다.

### 아키텍처 플로우

```
초기 실험 (Random Sampling)
    ↓
하이퍼파라미터 조합 A, B, C 테스트
    ↓
성능 결과 수집 및 분석
    ↓
베이지안 최적화 모델 학습
    ↓
다음 최적 조합 예측 및 선택
    ↓
새로운 실험 실행
    ↓
결과 피드백 → 모델 업데이트
    ↓
수렴까지 반복
```

### Trade-offs 고려사항

**베이지안 최적화 장점**:
- 이전 실험 결과를 활용한 지능적 탐색
- 적은 실험 횟수로 최적값 발견 가능
- 시간과 비용 효율성이 뛰어남
- 복잡한 파라미터 공간에서도 효과적

**베이지안 최적화 단점**:
- 초기 몇 번의 실험은 랜덤하게 진행
- 매우 간단한 경우 그리드 검색이 더 직관적일 수 있음

**그리드 검색 장점**:
- 모든 조합을 체계적으로 탐색
- 결과가 완전히 예측 가능

**그리드 검색 단점**:
- 파라미터 수가 증가하면 지수적으로 실험 수 증가
- 시간과 비용이 매우 많이 소요됨
- 비효율적인 탐색

**랜덤 검색 장점**:
- 구현이 간단함
- 그리드 검색보다는 효율적

**랜덤 검색 단점**:
- 이전 결과를 활용하지 못함
- 여전히 많은 실험이 필요

## 🔍 주요개념

### 하이퍼파라미터 튜닝 방법론 비교

- **그리드 검색 (Grid Search)**: 사전 정의된 모든 파라미터 조합을 체계적으로 테스트하는 방법. 완전하지만 매우 비효율적
- **랜덤 검색 (Random Search)**: 파라미터 공간에서 무작위로 조합을 선택하여 테스트. 그리드 검색보다 빠르지만 여전히 비효율적
- **베이지안 최적화 (Bayesian Optimization)**: 이전 실험 결과를 바탕으로 가장 유망한 다음 조합을 예측하여 테스트. 가장 효율적인 방법

### 실전 적용

- **이미지 분류 모델**: learning rate, batch size, hidden layer 수 등을 자동으로 튜닝하여 정확도 향상
- **시계열 예측**: DeepAR 모델의 context length, prediction length, embedding dimension 최적화
- **자연어 처리**: BERT 모델의 learning rate, warmup steps, dropout rate 등을 효율적으로 탐색

## 📝 관련 문제

**Question:** A data scientist needs to optimize hyperparameters for a custom machine learning algorithm deployed on Amazon SageMaker. The algorithm has 5 hyperparameters with continuous ranges, and the scientist wants to minimize training time while finding optimal values. Which approach should be used?

**Options:**

- A) Grid search across all possible parameter combinations
- B) Random search with 1000 random parameter combinations
- C) SageMaker automatic hyperparameter tuning with Bayesian optimization
- D) Manual tuning based on domain expertise
- E) Sequential search testing one parameter at a time

**정답: C) SageMaker automatic hyperparameter tuning with Bayesian optimization**

💡 추가 설명:

- **Option A (Grid Search)** - 5개 연속형 파라미터에 대해 그리드 검색은 실질적으로 불가능하며 매우 비효율적
- **Option B (Random Search)** - 1000번의 무작위 시도는 이전 결과를 활용하지 못해 비효율적
- **Option D (Manual Tuning)** - 도메인 전문성에 의존하며 객관적이지 않고 확장성이 떨어짐
- **Option E (Sequential Search)** - 파라미터 간 상호작용을 고려하지 못하고 매우 느림