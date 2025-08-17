---

title: recommendation-neptune-collaborative
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/recommendation
- service/neptune
- algorithm/collaborative-filtering
- usecase/streaming-service
- data/user-interactions
aliases: ["collab-filtering", "cf-neptune", "movie-rec"]

---

# Amazon Neptune에서 Collaborative Filtering을 활용한 영화 추천 시스템

## 🎯 핵심 포인트

수백만 사용자의 스트리밍 데이터가 Amazon Neptune에 저장된 경우, Collaborative Filtering 알고리즘을 활용하여 효과적인 영화 추천 시스템과 고객 패턴 분석을 구현할 수 있다.

## 📝 설명

### Collaborative Filtering이 영화 추천에 적합한 이유

Collaborative Filtering은 (사용자, 아이템, 평점) 튜플을 기반으로 작동하여 주어진 데이터 구조에 완벽하게 적합합니다. 이 알고리즘은 콘텐츠 자체의 특성이 아닌 사용자 간의 상호작용 패턴을 분석하여 추천을 생성하며, Amazon Neptune의 그래프 데이터베이스 특성과 잘 어울립니다.

핵심 작동 원리는 비슷한 취향을 가진 사용자들이 아직 경험하지 않은 콘텐츠에도 유사한 반응을 보일 것이라는 가정에 기반합니다. 수백만 사용자의 스트리밍 히스토리, 평점, 상호작용 데이터가 축적되면 이러한 패턴 분석이 더욱 정확해집니다.

### 아키텍처 플로우

```
사용자 상호작용 데이터 (인구통계, 스트리밍 히스토리, 평점)
                    ↓
            Amazon Neptune (그래프 DB)
                    ↓
        Collaborative Filtering 모델 학습
                    ↓
        유사 사용자 그룹 식별 및 패턴 분석
                    ↓
    개인화된 영화 추천 + 고객 패턴 인사이트
```

### Trade-offs 고려사항

**Collaborative Filtering 장점**:
- 다양성: 추천 아이템들의 차별화된 선택
- 세렌디피티: 예상치 못한 훌륭한 추천의 놀라움
- 참신성: 사용자가 알지 못했던 새로운 콘텐츠 발견
- 콘텐츠 분석 불필요: 영화 메타데이터 없이도 작동

**Collaborative Filtering 단점**:
- 높은 계산 비용과 복잡성
- 콜드 스타트 문제: 신규 사용자/아이템에 대한 추천 어려움
- 대용량 상호작용 데이터 필요

**Content-based Filtering 장점**:
- 신규 아이템에 대한 즉시 추천 가능
- 사용자 설명 가능한 추천 근거

**Content-based Filtering 단점**:
- 제한된 다양성과 참신성
- 영화 메타데이터 의존성
- 사용자 취향 변화 반영 어려움

## 🔍 주요개념

### 추천 시스템 알고리즘 비교

- **Collaborative Filtering**: 사용자-아이템 상호작용 기반, 유사 사용자 패턴 활용
- **Content-based Filtering**: 아이템 속성 기반, 사용자 과거 선호도 분석
- **LDA (Latent Dirichlet Allocation)**: 토픽 모델링, 문서에서 주제 발견
- **PCA (Principal Component Analysis)**: 차원 축소, 단독 사용 불가
- **RCF (Random Cut Forest)**: 이상 탐지 전용

### 실전 적용

- **Netflix/Amazon Prime**: 수백만 사용자 시청 패턴으로 개인화 추천
- **Spotify**: 음악 청취 히스토리 기반 플레이리스트 생성
- **E-commerce**: 구매 이력 기반 상품 추천 및 교차 판매

## 📝 관련 문제

**Question:** A movie streaming service is receiving millions of user data that are stored in Amazon Neptune. The data contains information about demographics, user interactions, streaming history, and movie ratings. A Machine Learning Specialist has been tasked to improve user experience by building a model that gives movie recommendations as well as identify customer shopping patterns, trends, and preferences. Which approach should the Specialist take?

**Options:**

- A) Identify patterns in the user data by using a model based on the Latent Dirichlet Allocation (LDA) algorithm
- B) Identify patterns in the user data by using a model based on the Collaborative filtering algorithm
- C) Identify patterns in the user data by using a model based on the Random Cut Forest (RCF) algorithm
- D) Identify patterns in the user data by using a model based on the Principal Component Analysis (PCA) algorithm

**정답: B) Collaborative filtering algorithm**

💡 추가 설명:

- **LDA** - 토픽 모델링 알고리즘으로 문서에서 주제 발견에 사용, 영화 추천에 부적합
- **RCF** - 이상 탐지 전용 알고리즘으로 추천 시스템 구축 불가능
- **PCA** - 차원 축소 기법으로 단독으로는 머신러닝 태스크 해결 불가능, 다른 알고리즘과 조합 필요