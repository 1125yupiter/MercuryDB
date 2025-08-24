---
title: classification-recall-imbalanced-health
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/classification
- technique/class-balancing
- constraint/imbalanced-data
- industry/healthcare
- metric/recall
aliases: ["imbalanced-classification", "recall-improvement", "medical-ml"]

---

# 불균형 데이터셋에서 분류 모델의 Recall 개선 - 의료 진단 사례

## 🎯 핵심 포인트

심각한 클래스 불균형이 있는 의료 데이터에서 높은 정확도에도 불구하고 recall이 낮은 경우, SMOTE와 같은 오버샘플링 기법과 cost-sensitive learning을 적용하여 False Negative를 줄이고 소수 클래스 탐지 성능을 향상시킬 수 있다.

## 📝 설명

### 클래스 불균형이 의료 진단 모델에 미치는 영향

갑상선암 예측과 같은 의료 진단 모델에서는 일반적으로 정상(음성) 케이스가 질병(양성) 케이스보다 압도적으로 많습니다. 이런 불균형 데이터에서는 모델이 다수 클래스에 편향되어 높은 정확도를 보이면서도 소수 클래스(암 케이스) 탐지에서 낮은 recall을 보이는 현상이 발생합니다.

**문제 상황 분석**:
- 데이터 비율: 900개 음성 vs 100개 양성 (9:1 불균형)
- 정확도: 90% (높음)
- Recall: 매우 낮음 (False Negative 다수 발생)
- 결과: 실제 암 환자를 정상으로 잘못 분류

### 아키텍처 플로우

```
원본 불균형 데이터 (900:100)
         ↓
    [데이터 전처리]
         ↓
┌─────────────────────┬─────────────────────┐
│   SMOTE 오버샘플링     │  Cost-sensitive     │
│   (데이터 균형 조정)    │  Learning 적용       │
│                     │  (임계값/가중치 조정).   │
└─────────────────────┴─────────────────────┘
         ↓
    [모델 학습/예측]
         ↓
    Recall 개선된 결과
```

### Trade-offs 고려사항

**SMOTE 오버샘플링 장점**:
- 클래스 불균형을 직접적으로 해결
- 소수 클래스에 대한 모델 민감도 향상
- 합성 데이터로 다양성 증가

**SMOTE 오버샘플링 단점**:
- 과적합 위험 증가
- 노이즈 데이터 생성 가능성
- 계산 비용 증가

**Cost-sensitive Learning 장점**:
- 원본 데이터 유지하면서 성능 개선
- 비즈니스 요구사항 직접 반영 가능
- 임계값 조정으로 precision-recall 균형 조절

**Cost-sensitive Learning 단점**:
- Precision 감소 가능성 (False Positive 증가)
- 하이퍼파라미터 튜닝 복잡성
- 도메인 지식 필요

**부적절한 접근법들**:
- **음성 인스턴스 증강**: 기존 불균형을 더욱 악화시킴
- **단순 앙상블 방법**: 클래스 분포 문제를 근본적으로 해결하지 못함
- **특성 선택만으로 해결**: 불균형 문제의 직접적 해결책이 아님

## 🔍 주요개념

### 핵심 메트릭 비교

- **Accuracy**: 전체 예측 중 맞춘 비율 (불균형 데이터에서 오해의 소지)
- **Recall (Sensitivity)**: 실제 양성 중 올바르게 예측한 비율 (의료에서 중요)
- **Precision**: 양성 예측 중 실제 양성인 비율
- **F1-Score**: Precision과 Recall의 조화평균

### 실전 적용

- **희귀 질병 진단**: 암, 심장병 등 생명과 직결된 질병 탐지
- **사기 거래 탐지**: 정상 거래 대비 사기 거래 비율이 극도로 낮은 상황
- **품질 불량 검출**: 제조업에서 정상품 대비 불량품 비율이 낮은 경우

## 📝 관련 문제

**Question:** A healthcare organization is developing a machine learning model aimed at predicting thyroid cancer occurrences. The training dataset comprises 900 instances labeled as negative (no cancer) and 100 instances labeled as positive (cancer). Despite achieving an accuracy of 90%, the model demonstrates significantly low recall for detecting positive instances. Which TWO of the following approaches should be considered to enhance the model's overall performance, specifically improving recall?

**Options:**

- A) Incorporate feature selection techniques to remove irrelevant features
- B) Apply data augmentation techniques to the negative instances
- C) Implement cost-sensitive learning approach or adjust classification threshold
- D) Utilize SMOTE or other over-sampling methods to increase positive instances
- E) Employ ensemble methods like boosting to improve model sensitivity

**정답: C) Cost-sensitive Learning과 D) SMOTE 오버샘플링**

💡 추가 설명:

- **Option A (특성 선택)** - 특성이 많은 데이터셋에서만 효과적이며, 클래스 불균형 문제의 직접적 해결책이 아님
- **Option B (음성 데이터 증강)** - 이미 많은 음성 클래스를 더 늘리면 기존 불균형을 악화시켜 recall을 더욱 감소시킴
- **Option E (앙상블 방법)** - 어려운 인스턴스에 집중하지만 클래스 분포 문제를 근본적으로 해결하지 못해 심각한 불균형에서는 효과 제한적