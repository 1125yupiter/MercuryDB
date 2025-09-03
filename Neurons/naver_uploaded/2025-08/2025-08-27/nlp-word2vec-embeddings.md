---

title: nlp-word2vec-embeddings
created: 2025-08-19
modified: 2025-08-19
tags:
- nlp/word-embeddings
- technique/word2vec
- representation/dense-vectors
- method/neural-network
- concept/semantic-similarity
aliases: ["word-embeddings", "word2vec", "dense-vectors"]

---

# NLP Word2Vec을 활용한 단어 임베딩 생성

## 🎯 핵심 포인트

단어를 의미적 관계가 반영된 밀집 벡터로 표현해야 하는 경우 NLP에서, Word2Vec 기법을 활용할 수 있다.

## 📝 설명

### Word2Vec이 단어 임베딩 생성에 적합한 이유

Word2Vec은 단어를 고차원의 밀집 벡터(dense vector)로 변환하는 신경망 기반 기법입니다. 이 방법은 "비슷한 맥락에서 등장하는 단어들은 비슷한 의미를 갖는다"는 분포 가설을 기반으로 작동합니다. 신경망을 통해 단어 간의 관계를 학습하여, 의미적으로 유사한 단어들이 벡터 공간에서 가까운 위치에 배치되도록 합니다.

### 아키텍처 플로우

```
텍스트 코퍼스 → 단어-맥락 쌍 생성 → 신경망 학습 (Skip-gram/CBOW) → 밀집 벡터 생성 → 의미적 관계 캡처
     ↓              ↓                    ↓                    ↓                ↓
  "The cat"    → (cat, [the, sat])  → 가중치 업데이트    → [0.2, -0.5, 0.8...]  → king-man+woman=queen
```

### Trade-offs 고려사항

**Word2Vec 장점**:
- 의미적 관계를 벡터 연산으로 표현 (king - man + woman ≈ queen)
- 고정 크기 밀집 벡터로 효율적 저장 및 연산
- 전이 학습 가능 (사전 훈련된 모델 활용)
- 단어 유사도 측정 및 클러스터링 가능

**Word2Vec 단점**:
- 대용량 코퍼스와 긴 훈련 시간 필요
- 문맥에 따른 다의어 구분 불가 (bank: 은행 vs 강둑)
- Out-of-vocabulary 단어 처리 한계
- 단어 순서 정보 손실

**Bag-of-words 장점**:
- 구현이 간단하고 해석 용이
- 빠른 처리 속도
- 메모리 사용량 예측 가능

**Bag-of-words 단점**:
- 희소 벡터로 차원의 저주 문제
- 단어 순서와 의미 관계 무시
- 동의어와 유사어 구분 불가

## 🔍 주요개념

### 핵심 개념 비교

- **Skip-gram**: 중심 단어로부터 주변 단어들을 예측하는 방식. 희소한 단어에 대해 더 나은 표현 학습
- **CBOW (Continuous Bag of Words)**: 주변 단어들로부터 중심 단어를 예측하는 방식. 빈번한 단어에 대해 더 나은 표현 학습
- **Negative Sampling**: 모든 단어에 대해 소프트맥스를 계산하는 대신 일부 부정 샘플만 사용하여 학습 효율성 향상

### 실전 적용

- **검색 엔진**: 쿼리와 문서 간 의미적 유사도 측정으로 검색 정확도 향상
- **추천 시스템**: 사용자 선호 상품과 유사한 제품 벡터 공간에서 탐색
- **감정 분석**: 단어 임베딩을 특성으로 사용하여 텍스트 감정 분류 모델 구축

## 📝 관련 문제

**Question:** A company wants to build a recommendation system that can understand semantic relationships between product descriptions. They need to convert words into numerical representations that capture meaning and enable similarity calculations. Which technique would be most appropriate for creating dense vector representations of words?

**Options:**

- A) Bag-of-words model
- B) Principal Component Analysis (PCA)
- C) Word2Vec
- D) Singular Value Decomposition (SVD)
- E) None of the above

**정답: C) Word2Vec**

💡 추가 설명:

- **A) Bag-of-words** - 희소 벡터를 생성하며 단어 간 의미적 관계를 포착하지 못함
- **B) PCA** - 차원 축소 기법이지만 단어 임베딩 생성을 위해 특별히 설계되지 않음
- **D) SVD** - 행렬 분해 기법으로 차원 축소에 사용되지만 의미적 관계 학습에 특화되지 않음
- **E) None of the above** - Word2Vec이 선택지에 포함되어 있으므로 부적절함