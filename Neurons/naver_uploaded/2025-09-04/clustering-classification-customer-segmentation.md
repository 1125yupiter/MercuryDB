---
title: clustering-classification-customer-segmentation
created: 2025-08-23
modified: 2025-08-23
tags:
- method/unsupervised
- method/supervised
- algorithm/kmeans
- algorithm/xgboost
- usecase/customer-segmentation
aliases: ["clustering", "classification", "customer-segmentation"]

---

# 클러스터링 vs 분류: 고객 세그먼테이션에서의 머신러닝 접근법

## 🎯 핵심 포인트

고객 세그먼트의 특성을 이해해야 하는 경우 클러스터링에서, 숨겨진 패턴을 발견하여 소셜미디어 타겟팅에 활용할 수 있다.

## 📝 설명

### 클러스터링이 고객 세그먼트 발견에 적합한 이유

클러스터링은 비지도학습으로 레이블 없이 데이터의 자연스러운 패턴을 발견할 수 있다. 고객 데이터에서 나이, 소득, 반려동물 수, 클레임 횟수 등의 특성을 벡터로 변환하고, 유클리드 거리나 맨하탄 거리를 계산하여 유사한 고객들을 자동으로 그룹화한다. 이 과정에서 사람의 편견이 개입하지 않고 데이터가 스스로 말하는 패턴을 찾아낸다.

### 아키텍처 플로우

```
고객 데이터 수집
    ↓
벡터화 ([나이, 소득, 반려동물수, 클레임횟수])
    ↓
정규화 (0~1 사이 스케일 조정)
    ↓
거리 계산 (유클리드 거리: √[(x₁-x₂)² + (y₁-y₂)²])
    ↓
K-Means 클러스터링 실행
    ↓
세그먼트 특성 분석 (리텐션율, 클레임 패턴 등)
    ↓
소셜미디어에서 유사 프로필 타겟팅
```

### Trade-offs 고려사항

**클러스터링 장점**:
- 사전 정의된 라벨 불필요 (비지도학습)
- 숨겨진 패턴과 예상치 못한 세그먼트 발견
- 사람의 편견 배제하고 객관적 분석
- 자연스러운 고객 그룹 형성

**클러스터링 단점**:
- 최적 클러스터 수 결정의 어려움
- 해석의 주관성 (그룹의 의미 부여)
- 실시간 분류 시 추가 모델 필요

**XGBoost(분류) 장점**:
- 명확한 예측 확률 제공
- 특성 중요도 파악 용이
- 새로운 데이터에 대한 즉시 분류
- 복잡한 비선형 관계 학습 가능

**XGBoost(분류) 단점**:
- Ground Truth 라벨 필요 (지도학습)
- 라벨링 과정의 주관성과 편향 위험
- 기존에 정의된 범주에만 국한
- 새로운 패턴 발견의 한계

## 🔍 주요개념

### 거리 측정 방식 비교

- **유클리드 거리**: √[(x₁-x₂)² + (y₁-y₂)²] - 직선 거리, 가장 일반적
- **맨하탄 거리**: |x₁-x₂| + |y₁-y₂| - 격자 거리, 이상치에 robust
- **코사인 유사도**: 벡터 간 각도 측정, 크기보다 방향성 중시

### 지도학습 vs 비지도학습

- **지도학습(XGBoost)**: Ground Truth 필요, "이런 패턴 → 이런 라벨"
- **비지도학습(Clustering)**: 라벨 없이 패턴 발견, "비슷한 것끼리 묶어봐"

### 실전 적용

- **클러스터링 먼저**: 고가치 고객, 위험 고객, 안정 고객 등 자연스러운 세그먼트 발견
- **분류 모델 구축**: 발견된 세그먼트를 라벨로 활용하여 XGBoost 학습
- **소셜미디어 타겟팅**: 클러스터 특성과 유사한 프로필 검색 및 광고 집행

## 📝 관련 문제

**Question:** A marketing manager of a pet insurance business intends to conduct a focused social media marketing campaign to increase client acquisition. How could a machine learning model be used to detect prospective new clients on social media using existing customer data?

**Options:**

- A) Use regression on customer profile data to understand key characteristics of consumer segments
- B) Use clustering on customer profile data to understand key characteristics of consumer segments  
- C) Use a recommendation engine on customer profile data to understand key characteristics of consumer segments
- D) Use a decision tree classifier on customer profile data to understand key characteristics of consumer segments
- E) Use deep learning on customer profile data to understand key characteristics of consumer segments

**정답: B) Clustering**

💡 추가 설명:

- **A) Regression** - 연속적 수치값 예측에 사용, 고객 그룹화에는 부적절
- **C) Recommendation Engine** - 상품/콘텐츠 추천 목적, 세그먼트 특성 파악에는 직접적이지 않음
- **D) Decision Tree Classifier** - 지도학습으로 미리 정의된 라벨 필요, 새로운 세그먼트 발견 제한적
- **E) Deep Learning** - 복잡하지만 세그먼트 발견 자체에는 클러스터링이 더 직관적이고 해석 가능