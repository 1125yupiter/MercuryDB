---
title: classification-transferlearning-small-dataset
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/classification
- method/transfer-learning
- technique/data-augmentation
- constraint/small-dataset
- metric/accuracy
aliases: ["underfitting", "low-accuracy", "transfer-learning-low-data"]
---

# 이미지 분류: 전이 학습과 데이터 증강

## 🎯 핵심 포인트

훈련 및 검증 데이터에서 정확도가 낮아 과소적합이 발생한 경우, 훈련 데이터의 양을 늘려 모델의 편향을 줄여 정확도를 향상시킬 수 있다.

## 📝 설명

### 전이 학습이 소규모 데이터셋 분류에 적합한 이유

전이 학습은 대규모 데이터셋으로 사전 훈련된 모델의 가중치를 가져와 새로운 작업에 미세 조정하는 기술이다. 모델이 이미 선, 모서리, 질감과 같은 일반적인 시각적 특징을 학습했기 때문에, 처음부터 학습하는 것보다 훨씬 적은 데이터로도 높은 성능을 달성할 수 있다. 서킷보드의 불량 여부를 분류하는 상황에서 전이 학습은 제한된 데이터로도 빠르게 모델을 개발할 수 있게 한다.

그러나 본 사례에서는 300개의 이미지만으로 모델이 작업별 특징을 충분히 학습하지 못해 훈련 정확도 73%, 검증 정확도 72%로 과소적합이 발생했다. 이를 해결하기 위해 데이터 증강이나 추가 이미지 수집과 같은 전략이 필요하다.

### 아키텍처 플로우

```
사전 훈련된 모델 (파트너 회사)
      ↓
새로운 훈련 데이터 (300개 이미지)
      ↓
전이 학습 (최상단 레이어 재훈련)
      ↓
73% 정확도 (과소적합)
      ↓
새로운 이미지 추가 / 데이터 증강
      ↓
훈련 데이터셋 확장
      ↓
모델 재훈련
      ↓
정확도 향상 (목표: 95% 이상)
```

### Trade-offs 고려사항

**데이터 증강 장점**:

- **모델 일반화 능력 향상**: 다양한 변형(회전, 확대/축소 등)을 통해 실제 환경의 새로운 데이터에 대한 성능이 향상된다.
- **과소적합 해결**: 데이터 부족으로 인한 편향을 줄여 훈련 및 검증 정확도를 동시에 높인다.
- **비용 효율성**: 새로운 이미지를 수집하는 것보다 경제적이다.

**데이터 증강 단점**:

- **잠재적 과대적합**: 과도한 증강은 인공적인 패턴 학습으로 인해 과대적합을 유발할 수 있다.
- **계산 비용 증가**: 데이터셋 크기 증가로 훈련 시간이 길어질 수 있다.
- **제한된 다양성**: 원본 데이터의 다양성 한계를 완전히 극복하지 못한다.

**대안: 모델 복잡도 증가(레이어 추가) 장점**:

- **모델 표현력 향상**: 더 많은 레이어를 통해 복잡한 패턴을 학습할 수 있다.

**대안: 모델 복잡도 증가(레이어 추가) 단점**:

- **과대적합 위험**: 300개 이미지로는 복잡한 모델이 노이즈를 학습해 과대적합될 가능성이 높다. 이는 훈련 정확도는 높아지지만 검증 정확도가 낮아지는 결과를 초래할 수 있다.

## 🔍 주요개념

### 과소적합 vs. 과대적합

- **과소적합(Underfitting)**: 모델이 훈련 데이터를 충분히 학습하지 못해 편향이 높은 상태. 훈련 및 검증 데이터에서 모두 성능이 낮다. 데이터 부족, 단순한 모델, 또는 불충분한 훈련이 원인일 수 있다.
- **과대적합(Overfitting)**: 모델이 훈련 데이터의 노이즈까지 학습해 분산이 높은 상태. 훈련 데이터에서는 높은 성능을 보이지만 검증 데이터에서는 성능이 떨어진다. 복잡한 모델, 과도한 훈련, 또는 데이터 부족이 원인일 수 있다.

### 실전 적용

- **데이터 증강**: `ImageDataGenerator`를 사용해 이미지 회전, 확대/축소, 좌우 반전, 밝기 조절 등을 적용하여 데이터셋을 확장한다.
- **추가 데이터 수집**: 실제 공정 라인에서 불량/정상 서킷보드 이미지를 추가로 수집하여 데이터 다양성을 높인다.
- **전이 학습 최적화**: 확장된 데이터셋으로 사전 훈련된 모델을 재훈련하며, 동결된 레이어 수를 조정해 최적 성능을 찾는다.

## 📝 관련 문제

**Question:** An electronics manufacturing company is building an image classification model to detect whether a circuit board is defective or not. To decrease training time, a Machine Learning Specialist implemented transfer learning using 300 images on a pre-trained neural network obtained from a partner company. The company requires a model accuracy of no less than 95% to be feasible. The Specialist conducted a full grid search through a wide hyperparameter space. However, the optimal values returned only 73% and 72% accuracy on the training and validation set, respectively. How can the Specialist increase the model accuracy?

**Options:**

- A) Regularize the model by randomly dropping nodes.
- B) Increase the variance using a pre-trained neural network with more layers for transfer learning.
- C) Collect additional images and add them to the training data to decrease the bias, then retrain the model using transfer learning.
- D) Use Amazon SageMaker’s automatic model tuning to find the best hyperparameter values for the model.

**정답: C) Collect additional images and add them to the training data to decrease the bias, then retrain the model using transfer learning.**

💡 추가 설명:

- **오답 옵션 A**: 드롭아웃은 과대적합을 방지하는 정규화 기법이다. 현재 모델은 과소적합 상태이므로 드롭아웃은 성능을 더 저하시킬 수 있다.
- **오답 옵션 B**: 더 많은 레이어를 추가하면 모델 복잡도가 증가해 300개 이미지로는 노이즈를 학습할 가능성이 크다. 이는 과대적합으로 이어져 검증 정확도가 낮아질 수 있다.
- **오답 옵션 D**: 이미 광범위한 하이퍼파라미터 탐색을 수행했으므로, 문제는 하이퍼파라미터가 아니라 데이터 부족에 의한 과소적합이다. 자동 모델 튜닝으로 큰 성능 향상을 기대하기 어렵다.