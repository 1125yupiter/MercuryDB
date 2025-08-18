---

title: fraud-detection-smote-imbalanced
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/classification
- technique/smote
- constraint/imbalanced-data
- usecase/fraud-detection
- method/oversampling
aliases: ["SMOTE", "fraud-detection", "imbalanced-data"]

---

# 불균형 데이터에서 사기 거래 탐지를 위한 SMOTE 기법

## 🎯 핵심 포인트

사기 거래 데이터가 부족한 불균형 데이터셋에서 머신러닝 모델을 훈련할 경우, SMOTE(Synthetic Minority Oversampling Technique)를 사용하여 소수 클래스의 합성 데이터를 생성할 수 있다.

## 📝 설명

### SMOTE가 사기 탐지에 적합한 이유

SMOTE는 기존 소수 클래스 샘플들 사이의 특성을 보간하여 새로운 합성 데이터를 생성하는 오버샘플링 기법입니다. 단순한 데이터 복제와 달리, k-최근접 이웃을 이용해 기존 데이터 포인트들 간의 선형 보간을 통해 현실적인 새 샘플을 만들어냅니다.

### 아키텍처 플로우

```
원본 불균형 데이터
    ↓
소수 클래스(사기) 식별
    ↓
각 소수 클래스 샘플의 k-NN 탐색
    ↓
선택된 이웃과의 선형 보간
    ↓
합성 샘플 생성
    ↓
균형 잡힌 훈련 데이터셋
    ↓
머신러닝 모델 훈련
```

### Trade-offs 고려사항

**SMOTE 장점**:
- 소수 클래스의 특성 공간을 확장하여 모델 일반화 능력 향상
- 과적합 위험 감소 (동일 데이터 반복 없음)
- 기존 데이터의 분포와 일관성 있는 합성 샘플 생성

**SMOTE 단점**:
- 고차원 데이터에서 의미 없는 샘플 생성 가능
- 노이즈나 이상치 근처에서 부적절한 샘플 생성 위험
- 연산 복잡도 증가

**Random Oversampling 장점**:
- 구현이 간단하고 빠름
- 원본 데이터의 정확한 복사

**Random Oversampling 단점**:
- 과적합 위험 높음
- 새로운 정보 제공 없음

## 🔍 주요개념

### 샘플링 기법 비교

- **Oversampling**: 소수 클래스의 샘플 수를 증가시켜 균형 맞춤
- **Undersampling**: 다수 클래스의 샘플 수를 감소시켜 균형 맞춤
- **SMOTE**: 합성 데이터 생성을 통한 지능적 오버샘플링
- **ADASYN**: 밀도 기반 적응적 합성 샘플링

### 실전 적용

- 금융 서비스의 신용카드 사기 거래 탐지 (정상:사기 = 99.9:0.1)
- 의료 분야의 희귀 질병 진단 (정상:질병 = 95:5)
- 제조업의 불량품 검출 (정상:불량 = 98:2)

## 📝 관련 문제

**Question:** A Machine Learning Specialist is proposing a model that detects fraudulent transactions. However, the training data's information is not adequate because the company only has a low number of recorded fraudulent activities. Which is the MOST effective approach to generate fraud cases?

**Options:**

- A) Apply Synthetic Minority Oversampling Technique (SMOTE) on the fraudulent cases
- B) Apply random oversampling on the authentic cases
- C) Apply undersampling on the fraudulent cases
- D) Duplicate the entire training dataset and shuffle them after

**정답: A) Apply Synthetic Minority Oversampling Technique (SMOTE) on the fraudulent cases**

💡 추가 설명:

- **B) Random oversampling on authentic cases** - 이미 많은 정상 거래 데이터를 더 늘리면 불균형이 더 심해짐
- **C) Undersampling on fraudulent cases** - 적은 사기 데이터를 더 줄이면 학습할 정보가 부족해짐
- **D) Duplicate entire dataset** - 불균형 비율은 그대로 유지되어 근본적 해결이 안됨

---