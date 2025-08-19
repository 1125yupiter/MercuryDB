---

title: recommendation-sagemaker-factorization-sparse
created: 2025-08-19
modified: 2025-08-19
tags:
- service/sagemaker
- algorithm/factorization-machines
- format/recordio-float32
- structure/sparse-matrix
- usecase/movie-recommendation
aliases: ["FM", "Factorization-Machines", "SageMaker-FM"]

---

# Amazon SageMaker Factorization Machines을 활용한 영화 추천 시스템의 데이터 형식

## 🎯 핵심 포인트

사용자-영화 상호작용 데이터가 희소한 경우 SageMaker Factorization Machines에서, sparse matrix와 RecordIO float32 형식을 사용할 수 있다.

## 📝 설명

### SageMaker Factorization Machines이 추천 시스템에 적합한 이유

Factorization Machines는 특성 간 상호작용을 저차원 벡터로 표현하여 희소한 데이터에서도 효과적인 예측을 수행합니다. 각 특성(사용자 ID, 영화 ID)을 k차원 벡터로 임베딩하고, 벡터 내적을 통해 관계의 강도를 계산합니다.

**핵심 수식:**
```
y = w₀ + Σwᵢxᵢ + ΣΣ⟨vᵢ,vⱼ⟩xᵢxⱼ
```

여기서 `⟨vᵢ,vⱼ⟩`는 두 특성의 잠재 벡터 내적으로, 관계의 세밀한 강도를 실수값으로 표현합니다.

### 아키텍처 플로우

```
Raw Data → Sparse Matrix → RecordIO Float32 → SageMaker FM Training
   ↓              ↓              ↓                    ↓
User-Movie    [User_123]     Binary Format      Vector Embeddings
Interactions   [Movie_456]   [0.82, 0.15...]   User: [0.82, 0.15, 0.94]
  (sparse)     [Rating]      (float32)         Movie: [0.67, 0.89, 0.21]
```

### Trade-offs 고려사항

**Sparse Matrix + RecordIO Float32 장점**:
- 희소 데이터의 효율적 저장 및 처리
- Float32로 관계의 미묘한 차이 표현 가능
- SageMaker FM의 네이티브 지원 형식
- 이진 형식으로 빠른 I/O 성능

**Dense Matrix 단점**:
- 대부분이 0인 사용자-영화 상호작용을 비효율적으로 저장
- 메모리 사용량 급증
- 처리 속도 저하

**CSV Format 단점**:
- 텍스트 형식으로 파싱 오버헤드 발생
- SageMaker 최적화 혜택 미적용
- 대용량 데이터 처리 시 성능 제약

**Integer Format 단점**:
- SageMaker FM은 Float32만 지원 (기술적 제약)
- 관계의 세밀한 정도 표현 불가
- 벡터 내적 결과의 정밀도 손실

## 🔍 주요개념

### 데이터 형식 비교

- **Sparse Matrix**: 대부분이 0인 행렬을 효율적으로 저장하는 형식, 사용자-영화 상호작용의 본질적 특성
- **Dense Matrix**: 모든 원소를 저장하는 형식, 추천 시스템에는 비효율적
- **RecordIO Float32**: SageMaker의 이진 형식, 빠른 처리와 정밀한 수치 표현
- **Float32 vs Integer**: 연속적 관계 표현 vs 이산적 관계 표현의 차이

### 실전 적용

- 넷플릭스 영화 추천: 수백만 사용자와 수만 영화 간 희소한 평점 데이터
- 전자상거래 상품 추천: 사용자의 구매/조회 이력 기반 상품 예측
- 음악 스트리밍 추천: 사용자-아티스트-장르 간 복합적 상호작용 분석

## 📝 관련 문제

**Question:** A media company is building a movie recommendation system using Amazon SageMaker Factorization Machines to predict user ratings for movies. What is the required format for the training data to optimize the model's performance with sparse user-movie interaction data?

**Options:**

- A) A sparse matrix in RecordIO float32 format, comprising user IDs, movie IDs, ratings, and optional extra features
- B) A dense matrix in CSV format, where each row represents complete information for a user-movie rating across all movies
- C) A dense matrix in RecordIO float32 format, encapsulating user IDs, movie IDs, and ratings
- D) A sparse matrix in RecordIO integer format that includes user IDs, movie IDs, ratings, and potentially additional feature columns
- E) A JSON format with nested user-movie relationships and metadata

**정답: A) A sparse matrix in RecordIO float32 format, comprising user IDs, movie IDs, ratings, and optional extra features**

💡 추가 설명:

- **Option B** - Dense matrix는 희소한 사용자-영화 상호작용을 비효율적으로 표현하며, CSV는 처리 속도가 느림
- **Option C** - Dense matrix는 메모리 비효율성과 불필요한 0값 저장으로 부적합
- **Option D** - SageMaker Factorization Machines는 Float32만 지원하므로 integer format 사용 불가
- **Option E** - JSON은 SageMaker FM에서 지원하지 않는 형식이며 처리 오버헤드 발생