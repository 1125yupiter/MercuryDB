---
title: dimensionality-reduction-ml-pca-tsne
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/dimensionality-reduction
- technique/pca
- technique/tsne
- algorithm/unsupervised
- usecase/feature-selection
aliases: ["Dimensionality Reduction", "Feature Selection", "PCA", "t-SNE"]
---

# 머신러닝 대규모 데이터셋의 효율적인 차원 축소: PCA와 t-SNE

## 🎯 핵심 포인트

다수의 입력 변수를 가진 머신러닝 데이터셋을 간소화해야 하는 경우, PCA와 t-SNE를 통해 유의미한 변수만 선별하여 데이터 구조를 효율적으로 단순화하고 시각화할 수 있습니다.

## 📝 설명

### PCA와 t-SNE가 대규모 데이터셋 간소화에 적합한 이유

머신러닝에서 다차원 데이터셋은 분석의 복잡성을 높이고 모델의 성능에 부정적인 영향을 미칠 수 있습니다. 이럴 때 '차원 축소(Dimensionality Reduction)' 기법을 사용하여 데이터의 중요한 정보를 보존하면서 변수의 수를 줄일 수 있습니다. Principal Component Analysis (PCA)와 t-Distributed Stochastic Neighbor Embedding (t-SNE)는 이러한 차원 축소에 효과적인 대표적인 방법입니다.

-   **Principal Component Analysis (PCA)**
    PCA는 데이터셋 내의 차원(특성 수)을 줄이면서도 가능한 한 많은 정보를 유지하는 비지도 학습 알고리즘입니다. 이는 원래 특성들의 선형 조합으로 이루어진 새로운 특성인 '주성분(Principal Components)'을 찾아냅니다. 이 주성분들은 서로 상관관계가 없으며, 첫 번째 주성분은 데이터의 가장 큰 분산을 설명하고, 그 다음 주성분들이 그 다음으로 큰 분산을 설명하는 방식으로 구성됩니다.
    *   **작동 방식**: 데이터의 공분산 행렬을 기반으로 고유 벡터(Eigenvector)와 고유 값(Eigenvalue)을 계산하여 데이터의 분산이 가장 큰 방향을 찾습니다. 이 방향이 주성분이 되며, 높은 고유 값을 가진 주성분을 선택하여 차원을 축소합니다.
    *   **적용 고려사항**: 선형 변환이므로 데이터에 선형 관계가 잘 나타날 때 효과적입니다. 대규모 데이터셋에도 계산 효율이 좋습니다.

-   **t-Distributed Stochastic Neighbor Embedding (t-SNE)**
    t-SNE는 고차원 데이터를 2차원 또는 3차원과 같은 저차원 공간에 매핑하여 시각화하고 탐색하기 위한 비선형 차원 축소 알고리즘입니다. PCA와 달리 t-SNE는 데이터 포인트 간의 고차원 공간에서의 유사도를 저차원 공간에서도 최대한 보존하는 데 중점을 둡니다. 이는 특히 데이터 내의 복잡한 군집 구조나 패턴을 시각적으로 파악하는 데 매우 유용합니다.
    *   **작동 방식**: 데이터 포인트 간의 유사도를 확률 분포로 나타내어, 고차원 공간과 저차원 공간에서의 유사도 분포 간의 Kullback-Leibler (KL) 발산 값을 최소화하는 방식으로 최적화를 수행합니다.
    *   **적용 고려사항**: 비선형 특성을 잘 포착하며 시각화에 강력하지만, PCA보다 계산 비용이 높고 대규모 데이터셋에 직접 적용하기 어려울 수 있습니다. 또한, 실행할 때마다 결과가 달라질 수 있어 안정적인 시각화를 위해서는 파라미터 튜닝이 중요합니다.

### 차원 축소에 부적합한 다른 방법들

-   **Min-max scaling**: 데이터의 값을 특정 범위(예: 0~1)로 정규화하는 기법입니다. 이는 데이터의 스케일을 조정할 뿐 차원을 줄이지 않습니다.
-   **One-hot encoding**: 범주형 데이터를 컴퓨터가 이해할 수 있는 이진 벡터 형태로 변환하는 기법입니다. 이는 원래 특성의 수를 오히려 늘려 차원을 증가시킬 수 있습니다.
-   **Random cropping**: 이미지 데이터 증강 기법 중 하나로, 이미지의 일부분을 무작위로 잘라내는 방법입니다. 차원 축소와는 관련이 없습니다.

### 아키텍처 플로우
원시 고차원 데이터 (다수의 입력 변수)
    |
    V
데이터 전처리: 결측치 처리, 정규화 등
    |
    V
차원 축소: PCA 또는 t-SNE 적용
    |
    V
간소화된 저차원 데이터 (유의미한 변수만 선별)
    |
    V
모델 학습 또는 데이터 시각화


### Trade-offs 고려사항

**PCA 장점**:

-   선형 차원 축소로 데이터의 전체 분산을 최대한 보존합니다.
-   원래 변수들로부터 독립적인 새로운 변수(주성분)를 생성하여 다중 공선성 문제를 줄입니다.
-   계산 효율성이 높아 대규모 데이터셋에도 비교적 빠르게 적용 가능합니다.

**PCA 단점**:

-   비선형적인 데이터 패턴이나 복잡한 관계를 잘 포착하지 못할 수 있습니다.
-   새롭게 생성된 주성분은 원래 변수의 복합적인 조합이므로 해석하기 어려울 수 있습니다.

**t-SNE 장점**:

-   비선형 차원 축소로 데이터 내의 복잡한 군집 구조나 지역적인 패턴을 효과적으로 시각화합니다.
-   고차원 데이터의 잠재적인 구조를 저차원 공간에서 잘 표현하여 인사이트를 제공합니다.

**t-SNE 단점**:

-   계산 비용이 높아 매우 큰 데이터셋에는 직접 적용하기 어렵거나 오랜 시간이 소요됩니다.
-   새로운 데이터 포인트가 추가될 때마다 전체를 다시 계산해야 하므로 실시간 적용이 어렵습니다.
-   perplexity 값 등 초기 파라미터 설정에 따라 결과가 크게 달라질 수 있습니다.

**Min-max scaling**:
-   **장점**: 데이터 스케일을 특정 범위로 조정하여 모델 학습의 안정성을 높입니다.
-   **단점**: 차원 축소의 목적이 아닌 정규화 기법입니다.

**One-hot encoding**:
-   **장점**: 범주형 데이터를 모델이 처리할 수 있는 수치형 형태로 변환합니다.
-   **단점**: 차원의 수가 증가하여 데이터의 희소성을 높이고 모델 복잡도를 증가시킬 수 있습니다.

**Random cropping**:
-   **장점**: 이미지 데이터의 양을 늘려 모델의 일반화 성능을 향상시키는 데이터 증강 기법입니다.
-   **단점**: 차원 축소와는 전혀 관련이 없는 기술입니다.

## 🔍 주요개념

### 핵심 개념 비교

-   **Principal Component Analysis (PCA)**: 데이터의 분산을 최대로 보존하는 직교 변환을 통해 선형적으로 차원을 축소하는 비지도 학습 알고리즘입니다. 주로 데이터의 노이즈 감소, 저장 공간 효율화, 모델 성능 향상에 활용됩니다.
-   **t-Distributed Stochastic Neighbor Embedding (t-SNE)**: 고차원 데이터 포인트 간의 유사성을 저차원 공간에서 유지하며 비선형적으로 차원을 축소하는 알고리즘입니다. 주로 복잡한 데이터의 군집 구조를 시각화하고 탐색하는 데 사용됩니다.

### 실전 적용

-   **대규모 이미지/텍스트 데이터의 특징 벡터 차원 축소**: 이미지의 픽셀 값이나 텍스트의 임베딩 벡터처럼 고차원적인 데이터를 PCA를 사용하여 효율적으로 모델에 입력할 수 있도록 차원을 줄입니다.
-   **복잡한 고객 데이터 분석을 위한 주요 패턴 식별**: 수많은 고객 행동 데이터(구매 이력, 웹사이트 방문 기록 등)에서 PCA를 통해 핵심적인 행동 패턴을 나타내는 주성분을 찾아내 고객 세분화에 활용할 수 있습니다.
-   **유전체 데이터 또는 의료 이미지 데이터의 시각화**: 고차원의 유전체 데이터나 의료 이미지 데이터에서 t-SNE를 적용하여 질병의 유형별 군집을 시각적으로 파악하고 이상 징후를 발견할 수 있습니다.

## 📝 관련 문제

**Question:** A Machine Learning Specialist is preparing a dataset that contains a multitude of input variables. The Scientist wants to simplify the structure of her data by keeping only the significant variables. Which methods could the Scientist apply? (Select TWO.)

**Options:**

-   A) t-distributed stochastic neighbor embedding (t-SNE)
-   B) One-hot encoding
-   C) Random cropping
-   D) Principal Component Analysis (PCA)
-   E) Min-max scaling

**정답: A) t-distributed stochastic neighbor embedding (t-SNE), D) Principal Component Analysis (PCA)**

💡 추가 설명:

-   **A) t-distributed stochastic neighbor embedding (t-SNE)** - 비선형 차원 축소 기법으로 고차원 데이터의 복잡한 구조를 저차원에서 시각화하거나 간소화하는 데 효과적입니다.
-   **D) Principal Component Analysis (PCA)** - 선형 차원 축소 기법으로 데이터의 분산을 최대한 보존하면서 변수의 수를 줄여 데이터 구조를 간소화합니다.
-   **B) One-hot encoding** - 범주형 변수를 수치형으로 변환하는 방법으로, 차원을 증가시킬 수 있어 부적합합니다.
-   **C) Random cropping** - 데이터 증강(Data Augmentation) 기법으로, 이미지의 일부분을 무작위로 잘라내는 것이며 차원 축소와는 무관합니다.
-   **E) Min-max scaling** - 데이터를 특정 범위로 정규화하는 스케일링 기법으로, 차원을 줄이는 것과는 관련이 없습니다.