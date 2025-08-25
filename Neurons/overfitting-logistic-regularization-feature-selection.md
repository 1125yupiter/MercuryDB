---
title: overfitting-logistic-regularization-feature-selection
created: 2025-08-25
modified: 2025-08-25
tags:
- problem/overfitting
- method/regularization
- technique/feature-selection
- constraint/interpretability
- usecase/customer-churn
aliases: ["overfitting-solution", "regularization-vs-feature-selection", "interpretable-ml"]

---

# 과적합 해결을 위한 정규화와 특성선택 기법

## 🎯 핵심 포인트

과적합이 발생한 고차원 데이터에서 해석 가능한 모델이 필요한 경우, L1 정규화와 재귀적 특성 제거를 통해 모델 복잡도를 줄이고 중요한 특성만을 선별할 수 있다.

## 📝 설명

### 과적합 문제 상황 분석

**문제 상황**:
- 100개의 연속형 수치 특성을 가진 고차원 데이터
- 훈련 세트와 검증 세트 간 성능 격차 → 과적합 발생
- 마케팅팀의 모델 해석 요구사항
- 로지스틱 회귀 모델 사용 (선형 모델)

**근본 원인**:
- 특성 수에 비해 상대적으로 부족한 샘플 수
- 불필요하거나 노이즈가 많은 특성들의 존재
- 모델 복잡도가 데이터의 복잡도를 초과

### 해결책 플로우

```
고차원 데이터 (100 특성)
         ↓
    과적합 발생 감지
         ↓
    ┌─── L1 정규화 ───┐
    │                │
    │  계수 축소       │  특성 자동 선택
    │  일반화 성능     │  (일부 계수 → 0)
    └─────────────────┘
         ↓
    ┌── RFE (재귀적 특성 제거) ──┐
    │                          │
    │  중요도 기반 특성 순위    │  점진적 제거
    │  최적 특성 집합 도출      │  교차검증 활용
    └──────────────────────────┘
         ↓
    해석 가능한 최적 모델
```

### Trade-offs 고려사항

**L1 정규화 (Lasso) 장점**:
- 자동 특성 선택: 불필요한 특성의 계수를 0으로 만듦
- 과적합 방지: 모델 복잡도 제어
- 해석성 유지: 선형 결합 구조 보존
- 계산 효율적: 단일 하이퍼파라미터 조정

**L1 정규화 단점**:
- 상관관계가 높은 특성들 중 임의로 하나만 선택할 수 있음
- 그룹으로 중요한 특성들을 모두 유지하기 어려움
- 최적 λ 값 찾기 위한 튜닝 필요

**재귀적 특성 제거 (RFE) 장점**:
- 체계적인 특성 선택: 중요도 기반 순위화
- 교차검증과 결합하여 최적 특성 수 결정
- 다양한 평가 지표 활용 가능
- 도메인 지식과 결합하여 해석 용이

**재귀적 특성 제거 단점**:
- 계산 비용이 높음 (여러 번의 모델 학습)
- 특성 간 상호작용 효과를 놓칠 수 있음
- 특성 수가 많을 때 시간 소요

**부적절한 대안들**:

**특성 추가**: 이미 과적합 상황에서 차원을 더 늘리면 문제 악화

**t-SNE**: 
- 비선형 변환으로 해석 불가능해짐
- 원본 특성과의 관계 추적 어려움
- 확률적 결과로 재현성 부족

**LDA (Linear Discriminant Analysis)**:
- 분류 알고리즘 교체이지 과적합 해결책 아님
- 현재 로지스틱 회귀 프레임워크와 별개 접근

## 🔍 주요개념

### 정규화 vs 특성선택 비교

- **L1 정규화 (Lasso)**: 계수에 제약을 가해 모델 복잡도 제어, 부차적으로 특성 선택 효과
- **L2 정규화 (Ridge)**: 계수 크기 감소시키지만 특성 제거하지 않음
- **재귀적 특성 제거**: 명시적으로 최고의 특성 부분집합을 찾는 방법
- **전진 선택법**: 특성을 하나씩 추가하며 성능 개선 확인

### 차원축소와의 차이점

- **PCA**: 선형 결합으로 새로운 차원 생성, 해석성 일부 유지
- **t-SNE**: 비선형 변환으로 시각화 목적, 해석성 완전 상실
- **특성 선택**: 원본 특성을 그대로 유지하여 완벽한 해석성 보장

### 실전 적용

- **마케팅 분석**: "고객 나이가 1세 증가하면 이탈 확률이 얼마나 변하는가?"
- **리스크 관리**: "신용점수 10점 하락이 대출 승인에 미치는 영향"
- **의료 진단**: "혈압 수치와 콜레스테롤 수치가 심장질환 위험에 미치는 개별 영향"

## 📝 관련 문제

**Question:** Using a dataset of 100 continuous numerical characteristics, a Data Scientist is developing a model to predict customer attrition. The Marketing department has offered no guidance on which characteristics are significant for churn prediction. The Marketing department want to interpret the model and determine the direct effect of important characteristics on the model's output. While training a logistic regression model, the Data Scientist notices a significant difference in the accuracy of the training and validation sets. Which techniques can the Data Scientist employ to optimize the model's performance and meet the demands of the Marketing team? (Select two.)

**Options:**

- A) Add L1 regularization to the classifier
- B) Add features to the dataset  
- C) Perform recursive feature elimination
- D) Perform t-distributed stochastic neighbor embedding (t-SNE)
- E) Perform linear discriminant analysis

**정답: A) L1 regularization, C) Recursive feature elimination**

💡 추가 설명:

- **Option B (특성 추가)** - 과적합 상황에서 차원을 더 늘리면 문제가 악화됨. 이미 100개 특성도 충분히 많은 상황
- **Option D (t-SNE)** - 비선형 변환으로 원본 특성과의 관계가 끊어져 마케팅팀이 해석할 수 없게 됨. 시각화 목적으로만 적합
- **Option E (LDA)** - 다른 분류 알고리즘으로 교체하는 것이지 현재 과적합 문제를 직접 해결하지 않음

**핵심 학습 포인트**:
- 과적합 = 모델 복잡도 > 데이터 복잡도 → 복잡도 감소 필요
- 해석 가능성 요구 = 선형성 유지 + 원본 특성 보존
- t-SNE는 비선형 모델에서 패턴 탐지용, 해석 가능한 모델링에는 부적합