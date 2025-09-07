---
title: classification-mlp-imbalanced-balancing  
created: 2025-08-24  
modified: 2025-08-24  
tags:  
- problem/classification  
- method/deep-learning  
- algorithm/neural-net  
- constraint/imbalanced-data  
- technique/class-balancing  
aliases: ["mlp recall", "class imbalance", "low recall", "fn issue"]
---

# 멀티클래스 분류에서 MLP Recall 개선을 위한 클래스 가중치 적용

## 🎯 핵심 포인트
멀티클래스 분류에서 소수 클래스의 **FN이 많아 Recall이 낮은 경우**, 손실 함수에 **클래스 가중치**를 적용하여 Recall을 높일 수 있다.

## 📝 설명

### MLP가 멀티클래스 분류 Recall 문제 해결에 적합한 이유
MLP(Multilayer Perceptron)는 비선형 패턴 학습에 강점이 있지만, 클래스 불균형 상황에서는 소수 클래스의 손실 기여도가 작아져 Recall이 낮아지는 문제가 있다. 이때 클래스별 가중치를 손실 함수에 부여하면, 소수 클래스의 FN에 더 큰 패널티가 적용되어 모델이 해당 클래스를 더 잘 탐지하도록 유도할 수 있다.  

이 방식은 모델 구조를 변경하지 않고도 적용 가능하며, 데이터 수집보다 빠르고 비용 효율적으로 Recall을 개선할 수 있다.

### 아키텍처 플로우
[입력 데이터]
↓
[MLP 모델]
↓
[손실 함수: 교차 엔트로피 + 클래스 가중치 적용]
↓
[모델 업데이트 - 소수 클래스 오류에 더 큰 패널티]
↓
[출력: Recall 개선]

### Trade-offs 고려사항

**MLP + Class Weight 장점**:
- 데이터 추가 수집 없이 Recall 개선 가능
- 손실 함수 레벨에서 단순하게 적용 가능
- FN 감소 → Recall 증가 효과 직접적  

**MLP + Class Weight 단점**:
- Precision 하락 가능 (FP 증가)
- 가중치 선택이 모델 성능에 민감  

**대안(XGBoost) 장점**:
- 불균형 데이터에 상대적으로 강건
- 트리 기반 모델이라 해석력 높음  

**대안(XGBoost) 단점**:
- Recall 개선을 보장하지 않음
- 문제 정의(딥러닝 기반 확장성)과 다소 불일치  

**대안(Anomaly Detection) 장점**:
- 극도로 희귀한 클래스 탐지엔 적합  

**대안(Anomaly Detection) 단점**:
- 멀티클래스 분류 문제 정의와 불일치  

## 🔍 주요개념

### FN과 Recall 관계
- **FN(False Negative)**: 실제 Positive인데 모델이 Negative로 분류  
- **Recall**:  
  \[
  \text{Recall} = \frac{TP}{TP+FN}
  \]  
  → FN이 많으면 Recall이 낮아짐  

### 클래스 가중치(Class Weight)
- **정의**: 손실 함수에서 클래스별 오류에 가중치를 부여  
- **특징**: 소수 클래스의 오류가 손실에 더 크게 반영 → Recall 향상  

### 실전 적용
- Keras: `model.fit(..., class_weight={0:w0, 1:w1, ...})`  
- PyTorch: `nn.CrossEntropyLoss(weight=torch.tensor([w0, w1, ...]))`  
- scikit-learn: `class_weight='balanced'` 옵션 사용  

## 📝 관련 문제

**Question:**  
A data scientist is training an MLP model on a multiclass dataset. The target class has low recall compared to others. The scientist has tried adjusting hidden layers, but results did not improve. What is the most appropriate immediate solution?  

**Options:**  
- A) Collect more data using Amazon Mechanical Turk  
- B) Train an anomaly detection model instead of an MLP  
- C) Train an XGBoost model instead of an MLP  
- D) Add class weights to the MLP's loss function and retrain  

**정답: D) Add class weights to the MLP's loss function and retrain**

💡 추가 설명:
- **A** - 시간·비용 소요, 즉각적이지 않음  
- **B** - 문제 정의(멀티클래스 분류)와 불일치  
- **C** - Recall 개선을 보장하지 않음, 단순 모델 교체로 해결 불가  
- **D** - 데이터 불균형 문제에 직접 대응, Recall 개선에 효과적 ✅  
