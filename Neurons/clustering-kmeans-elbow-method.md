---

title: clustering-kmeans-elbow-method
created: 2025-08-16
modified: 2025-08-16
tags:
- algorithm/kmeans
- method/elbow-method
- optimization/cluster-selection
- evaluation/distortion-score
- technique/hyperparameter-tuning
aliases: ["elbow-method", "k-selection", "cluster-optimization"]

---

# K-means 클러스터링에서 엘보우 방법을 통한 최적 K값 선정

## 🎯 핵심 포인트

클러스터 수 증가에 따른 성능 개선의 한계효용이 급격히 감소하는 변곡점에서, 복잡성과 성능의 최적 균형을 찾을 수 있다.

## 📝 설명

### 엘보우 방법(Elbow Method)이 K값 선정에 적합한 이유

엘보우 방법은 다양한 K값에 대해 K-means 클러스터링을 실행하고, 각 K값에서의 클러스터 내 제곱합(WCSS) 또는 왜곡 점수(Distortion Score)를 계산합니다. 이 지표들을 그래프로 그렸을 때 "팔꿈치" 모양의 변곡점이 나타나는데, 이 지점이 최적의 K값을 나타냅니다.

### 수학적 분석 과정

```
K값별 Distortion Score 변화량:
K=2 → K=3: 1.5 → 0.8 (감소량: 0.7)
K=3 → K=4: 0.8 → 0.4 (감소량: 0.4)  
K=4 → K=5: 0.4 → 0.25 (감소량: 0.15) ← 급격한 개선 효과 감소

변곡점 식별:
K=4에서 한계효용 급감 → 최적 클러스터 수
```

### Trade-offs 고려사항

**K=4 선택의 장점**:
- 성능 개선 대비 복잡성 증가가 균형적
- 과적합(overfitting) 위험 최소화
- 해석 가능성과 계산 효율성 확보
- 실용적인 클러스터 수로 비즈니스 적용 용이

**K=5+ 선택의 단점**:
- 한계효용 급격 감소 (0.15 vs 이전 0.4, 0.7)
- 불필요한 복잡성 증가
- 계산 비용 상승
- 노이즈에 민감한 세분화 위험

**K=3 이하 선택의 단점**:
- 아직 상당한 개선 여지 존재 (감소량 0.4, 0.7)
- 과소적합(underfitting) 가능성
- 데이터의 내재적 구조 미반영

## 🔍 주요개념

### 성능 지표 비교

- **Distortion Score**: 각 데이터 포인트와 해당 클러스터 중심 간 거리의 제곱합
- **Inertia**: 클러스터 내 제곱합(WCSS)과 동일 개념
- **Silhouette Score**: 클러스터 간 분리도와 클러스터 내 응집도 측정

### 실전 적용

- **마케팅 세그멘테이션**: 고객을 4개 그룹으로 분류하여 타겟팅 전략 수립
- **이미지 압축**: 색상을 4개 대표 값으로 줄여 압축률과 화질 균형 달성
- **유전자 분석**: 유전자 발현 패턴을 4개 클러스터로 분류하여 질병 하위 유형 식별

## 📝 관련 문제

**Question:** A Machine Learning Specialist has graphed the results of a K-means model fitted through a range of k-values. The distortion scores are: k=2 (1.5), k=3 (0.8), k=4 (0.4), k=5 (0.25), k=6 (0.2). Which k-value represents the optimal choice using the elbow method?

**Options:**

- A) k=2
- B) k=3  
- C) k=4
- D) k=5
- E) k=6

**정답: C) k=4**

💡 추가 설명:

- **k=2** - 개선 여지가 큰 상태 (1.5→0.8로 0.7 감소 가능)
- **k=3** - 여전히 상당한 개선 효과 존재 (0.8→0.4로 0.4 감소)
- **k=5** - 한계효용 급감으로 복잡성 대비 효과 미미 (0.4→0.25로 0.15만 감소)
- **k=6** - 과적합 위험, 실질적 개선 효과 없음 (0.25→0.2로 0.05만 감소)