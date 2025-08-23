---

title: nlp-preprocessing-word2vec-socialmedia
created: 2025-08-18
modified: 2025-08-18
tags:
- technique/tokenization
- technique/preprocessing
- method/word2vec
- data/social-media
- constraint/scalable
aliases: ["NLP preprocessing", "Word2Vec preprocessing", "tokenization", "stop words"]

---

# Word2Vec을 위한 소셜미디어 텍스트 전처리 기법

## 🎯 핵심 포인트

대용량 소셜미디어 포스트 데이터에서 Word2Vec 임베딩을 생성하는 경우 확장 가능한 전처리 기법으로 토큰화, 불용어 제거, 소문자 정규화를 적용할 수 있다.

## 📝 설명

### Word2Vec이 소셜미디어 텍스트 처리에 적합한 이유

Word2Vec은 단어들의 의미적 관계를 벡터 공간에서 학습하는 기법으로, 소셜미디어의 비정형 텍스트에서 패턴을 찾아내는 데 효과적입니다. 하지만 원시 텍스트 데이터를 바로 사용할 수는 없으며, 모델이 의미 있는 패턴을 학습할 수 있도록 체계적인 전처리가 필요합니다.

### 전처리 아키텍처 플로우

```
Raw Social Media Post
    ↓
[토큰화] → 단어 단위로 분할
    ↓
[소문자 정규화] → 대소문자 통일
    ↓
[불용어 제거] → 의미 없는 단어 제거
    ↓
Clean Token List → Word2Vec 입력
    ↓
Word Embeddings (벡터 표현)
```

### Trade-offs 고려사항

**확장 가능한 전처리 장점**:
- 대용량 데이터에 자동 적용 가능
- 일관된 규칙으로 처리하여 편향 최소화
- 계산 비용과 저장 공간 절약
- 모델 학습 속도 향상

**확장 가능한 전처리 단점**:
- 오타나 줄임말로 인한 정보 손실
- 맥락적 의미 변화 감지 불가
- 도메인 특수 표현 무시 가능성

**수동 전처리 장점**:
- 높은 정확도와 완성도
- 도메인 지식 반영 가능
- 특수한 경우 개별 처리

**수동 전처리 단점**:
- 대용량 데이터에 비현실적
- 일관성 부족 위험
- 높은 인건비와 시간 소요

## 🔍 주요개념

### 핵심 전처리 기법 비교

- **토큰화(Tokenization)**: 텍스트를 의미 있는 단위(보통 단어)로 분할하는 과정. Word2Vec의 기본 입력 형태를 만드는 필수 단계
- **불용어 제거(Stop Words Removal)**: "the", "and", "is" 같이 문법적 기능은 하지만 의미적 가치가 낮은 단어들을 제거하여 노이즈 감소
- **소문자 정규화(Lowercase Normalization)**: 대소문자를 통일하여 같은 단어가 다르게 인식되는 것을 방지

### 실전 적용

- **고객 리뷰 감정 분석**: 제품 리뷰에서 감정 표현 단어들의 벡터 관계 학습
- **소셜미디어 트렌드 분석**: 해시태그와 키워드 간의 연관성 파악
- **챗봇 자연어 이해**: 사용자 입력의 의도 파악을 위한 단어 임베딩 활용

## 📝 관련 문제

**Question:** A Machine Learning Specialist is developing a Natural Language Processing (NLP) application that processes a large set of collated social media posts. The Specialist's objective is to run Word2Vec to create embeddings of these posts to make various types of predictions. Below is an example of one of the posts: "The music EVENT last niht was so LIT!" Which combination of preprocessing techniques should be done to clean the data in a scalable manner? (Select THREE.)

**Options:**
- A) Use a list of English stop words as a reference in removing stop words
- B) Use one-hot encoding to all words in a post
- C) Fix the spelling of the word "niht" into "night"
- D) Normalize all characters and words in the post by converting them into lowercase
- E) Apply part-of-speech (POS) tagging to include only the noun and adjective words
- F) Use tokenization to split the post into words

**정답: A, D, F) 불용어 제거, 소문자 정규화, 토큰화**

💡 추가 설명:

- **Option B (원-핫 인코딩)** - Word2Vec은 단어 간 의미적 유사성을 학습하는데, 원-핫 인코딩은 희소하고 고차원적이어서 부적합
- **Option C (철자 교정)** - 개별 오타 수정은 확장성이 없으며, 모든 포스트의 오타를 찾아 수정하는 것은 비현실적
- **Option E (품사 태깅)** - 품사 태깅은 분석 단계의 기법이며, 동사나 부사 등 중요한 의미 정보를 제거할 위험이 있음

---