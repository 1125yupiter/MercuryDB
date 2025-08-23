---

title: classification-crossvalidation-imbalanced-stratified-binary
created: 2025-08-23
modified: 2025-08-23
tags:
- problem/classification
- technique/cross-validation
- constraint/imbalanced-data
- method/stratified-sampling
- evaluation/binary-classification
aliases: ["stratified-cv", "imbalanced-cv", "cross-validation"]

---

# Cross-Validation for Imbalanced Binary Classification

## 🎯 핵심 포인트

클래스 불균형이 심한 이진 분류 문제(3% vs 97%)에서 신뢰성 있는 모델 평가를 위해 Stratified K-Fold Cross-Validation을 사용할 수 있다.

## 📝 설명

### Stratified K-Fold가 클래스 불균형 문제에 적합한 이유

클래스 불균형이 심한 상황에서는 일반적인 K-Fold Cross-Validation을 사용할 경우 일부 fold에 양성 사례가 아예 포함되지 않거나 매우 적게 포함될 수 있다. Stratified K-Fold는 각 fold에서 원본 데이터의 클래스 비율(3%:97%)을 동일하게 유지하여 모든 fold에서 일관된 평가가 가능하도록 한다.

### 데이터 분할 프로세스

```
원본 데이터 (400명)
├─ Positive: 12명 (3%)
└─ Negative: 388명 (97%)

↓ Stratified K-Fold (k=5) 적용

Fold 1: [2-3개 Positive] + [77-78개 Negative] ≈ 3%:97%
Fold 2: [2-3개 Positive] + [77-78개 Negative] ≈ 3%:97%
Fold 3: [2-3개 Positive] + [77-78개 Negative] ≈ 3%:97%
Fold 4: [2-3개 Positive] + [77-78개 Negative] ≈ 3%:97%
Fold 5: [2-3개 Positive] + [77-78개 Negative] ≈ 3%:97%
```

### Trade-offs 고려사항

**Stratified K-Fold 장점**:
- 모든 fold에서 클래스 비율 일관성 유지
- 안정적인 성능 지표 계산 가능
- 각 fold에서 양성/음성 사례 모두 보장
- 과적합 방지를 위한 교차 검증 효과

**Stratified K-Fold 단점**:
- 일반 K-Fold보다 약간 더 복잡한 구현
- 클래스 수가 매우 적을 경우 완벽한 비율 유지 어려움

**일반 K-Fold 장점**:
- 구현이 단순함
- 계산 속도가 약간 더 빠름

**일반 K-Fold 단점**:
- 클래스 불균형 상황에서 일부 fold에 양성 사례 누락 가능
- 불안정한 성능 평가 결과
- 모델 훈련 시 편향된 학습 가능성

## 🔍 주요개념

### Cross-Validation 방법 비교

- **K-Fold CV**: 데이터를 k개 구간으로 나누어 교차 검증하는 기본 방법
- **Stratified K-Fold CV**: K-Fold에서 각 fold의 클래스 비율을 원본과 동일하게 유지
- **Leave-One-Out CV (LOOCV)**: 매번 1개 샘플만 테스트용으로 사용 (k=n)
- **Time Series CV**: 시간 순서를 고려한 과거→미래 방향 분할
- **Group K-Fold**: 특정 그룹이 train/test에 동시 포함되지 않도록 분할

### 실전 적용

- 의료 진단: 희귀 질병 발생률이 낮은 환자 데이터 분석
- 사기 탐지: 정상 거래 대비 사기 거래 비율이 현저히 낮은 금융 데이터
- 품질 관리: 불량률이 낮은 제조업 품질 검사 데이터

## 📝 관련 문제

**Question:** A data scientist is working on a binary classifier to identify patients with a rare disease affecting 3% of the population, using 400 randomly selected patient records. Which cross-validation approach should be used for reliable model evaluation?

**Options:**

- A) A k-fold cross-validation strategy with k=5
- B) A stratified k-fold cross-validation strategy with k=5
- C) A k-fold cross-validation strategy with k=5 and 3 repeats
- D) An 80/20 stratified split between training and validation
- E) Leave-one-out cross-validation (LOOCV)

**정답: B) A stratified k-fold cross-validation strategy with k=5**

💡 추가 설명:

- **Option A** - 일반 K-fold는 클래스 불균형 상황에서 일부 fold에 양성 사례가 누락될 수 있어 부적합
- **Option C** - 반복 수행으로 안정성은 높아지지만 근본적인 클래스 불균형 문제는 해결되지 않음
- **Option D** - 단일 분할로는 교차 검증의 장점을 활용할 수 없고 작은 데이터셋에서 신뢰성 부족
- **Option E** - 계산 비용이 매우 높고 클래스 불균형 문제를 해결하지 못함