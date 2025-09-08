---

title: clustering-tsne-highdimensional-exploration
created: 2025-08-25
modified: 2025-08-25
tags:
- problem/clustering
- method/tsne
- constraint/high-dimensional
- technique/dimensionality-reduction
- usecase/exploratory-analysis
aliases: ["tsne-clustering", "high-dim-clustering", "exploratory-clustering"]

---

# t-SNE를 활용한 고차원 데이터 클러스터링 탐색

## 🎯 핵심 포인트

고차원 데이터에서 자연스러운 그룹 패턴을 탐색하고자 하는 경우 t-SNE 차원축소와 시각화를 통해 데이터의 숨겨진 클러스터 구조를 효과적으로 발견할 수 있다.

## 📝 설명

### t-SNE가 고차원 데이터 탐색에 적합한 이유

t-SNE(t-Distributed Stochastic Neighbor Embedding)는 고차원 데이터의 비선형 구조를 2D/3D 공간으로 보존하며 매핑하는 차원 축소 기법입니다. 특히 수백 개 이상의 컬럼을 가진 데이터에서 숨겨진 클러스터 패턴을 시각적으로 탐지하는 데 탁월합니다.

**핵심 장점:**
- **비선형 관계 보존**: 복잡한 데이터 구조를 저차원에서도 유지
- **클러스터 구조 강화**: 유사한 데이터 포인트를 가깝게, 다른 포인트를 멀리 배치
- **탐색적 분석**: 사전 가정 없이 데이터 자체가 보여주는 패턴 발견

### 아키텍처 플로우

```
고차원 데이터 (수백~수천 컬럼)
     ↓
t-SNE 차원 축소 (2D/3D)
     ↓
산점도 시각화
     ↓
자연스러운 클러스터 패턴 관찰
     ↓
필요시 클러스터링 알고리즘 적용
```

### Trade-offs 고려사항

**t-SNE 장점**:
- 차원의 저주 문제 해결
- 복잡한 비선형 패턴 탐지
- 직관적인 시각적 결과
- 사전 클러스터 수 지정 불필요

**t-SNE 단점**:
- 계산 비용이 높음 (O(n²))
- 매번 다른 결과 (확률적 알고리즘)
- 거리 정보 왜곡 가능성
- 대용량 데이터에 부적합

**k-means 장점**:
- 빠른 계산 속도
- 명확한 클러스터 할당
- 해석하기 쉬운 결과
- 확장성이 좋음

**k-means 단점**:
- 고차원에서 성능 저하 (차원의 저주)
- 구형 클러스터만 탐지
- 클러스터 수 사전 지정 필요
- 이상치에 민감

## 🔍 주요개념

### 차원의 저주 vs 차원 축소

- **차원의 저주**: 고차원에서 모든 데이터 포인트가 비슷한 거리에 위치하여 클러스터링이 무의미해지는 현상
- **t-SNE**: 고차원의 지역적 구조를 저차원에서 보존하여 의미있는 패턴 발견

### 탐색적 vs 확정적 분석

- **탐색적 분석**: 데이터가 어떤 구조를 가지는지 사전 가정 없이 관찰 (t-SNE)
- **확정적 분석**: 특정 개수의 클러스터로 분할하여 각 그룹의 특성 분석 (k-means)

### 실전 적용

- **고객 세분화**: 수백 개의 구매 행동 변수에서 자연스러운 고객 그룹 발견
- **유전자 분석**: 수천 개의 유전자 발현 데이터에서 질병 패턴 탐지
- **이미지 분석**: 고차원 특징 벡터에서 유사 이미지 클러스터링

## 📝 관련 문제

**Question:** A Machine Learning Specialist is provided with a structured dataset including information about the buying behaviors of a company's customers. Each client is represented by thousands of data columns and hundreds of numerical columns. The Specialist's objective is to determine if these columns naturally group together across all consumers and to display the findings as rapidly as feasible. How should the Specialist tackle these tasks?

**Options:**

- A) Embed the numerical features using the t-distributed stochastic neighbor embedding (t-SNE) algorithm and create a scatter plot.
- B) Run k-means using the Euclidean distance measure for different values of k and create an elbow plot.
- C) Embed the numerical features using the t-distributed stochastic neighbor embedding (t-SNE) algorithm and create a line graph.
- D) Run k-means using the Euclidean distance measure for different values of k and create box plots for each numerical column within each cluster.

**정답: A) t-SNE + scatter plot**

💡 추가 설명:

- **Option B** - k-means는 고차원에서 차원의 저주로 인해 효과적이지 않으며, 엘보우 플롯은 최적 k값만 제시할 뿐 실제 데이터 구조는 보여주지 않음
- **Option C** - t-SNE는 적절하지만 선 그래프는 클러스터 시각화에 부적합 (시계열 데이터용)
- **Option D** - 수백 개 컬럼의 박스플롯 생성은 빠른 시각화 요구사항에 부적합하며 전체적인 패턴 파악이 어려움

---