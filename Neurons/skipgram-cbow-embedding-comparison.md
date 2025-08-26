---

title: skipgram-cbow-contextual-embedding
created: 2025-08-26
modified: 2025-08-26
tags:
- algorithm/skip-gram
- algorithm/cbow
- technique/word-embedding
- service/sagemaker-blazingtext
- problem/contextual-disambiguation
aliases: ["Skip-gram vs CBOW", "Word2Vec", "contextual-embedding"]

---

# Skip-gram vs CBOW를 활용한 문맥적 임베딩 생성

## 🎯 핵심 포인트

동일한 단어가 서로 다른 상황에서 사용되는 텍스트에서 문맥적 의미를 구분해야 하는 경우, Skip-gram 알고리즘이 CBOW보다 개별 문맥 관계를 독립적으로 학습하여 더 정밀한 의미 구분을 할 수 있다.

## 📝 설명

### Skip-gram이 문맥 구분에 적합한 이유

**수학적 구조의 차이**가 핵심입니다. CBOW는 주변 단어들을 평균화하여 중심 단어를 예측하는 반면, Skip-gram은 중심 단어로부터 각 주변 단어를 개별적으로 예측합니다.

**CBOW의 평균화 문제:**
```
Input: [w_{t-2}, w_{t-1}, w_{t+1}, w_{t+2}]
Hidden: h = (v_{w_{t-2}} + v_{w_{t-1}} + v_{w_{t+1}} + v_{w_{t+2}}) / 4
Output: P(w_t | context)
```

**Skip-gram의 개별 관계 학습:**
```
Input: w_t
Output: P(w_{t-2} | w_t), P(w_{t-1} | w_t), P(w_{t+1} | w_t), P(w_{t+2} | w_t)
```

### 아키텍처 플로우

```
CBOW 방식:
[주변단어들] → [평균벡터] → [중심단어 예측]
              ↓ 문맥 정보 희석

Skip-gram 방식:
[중심단어] → [개별 주변단어1 예측]
           → [개별 주변단어2 예측]
           → [개별 주변단어3 예측]
             ↓ 각 관계 독립 학습
```

### Trade-offs 고려사항

**Skip-gram 장점**:
- 각 (중심단어, 주변단어) 쌍을 독립적으로 최적화
- **의미 깊이가 깊은 단어들**의 문맥별 뉘앙스 세밀 구분
- 저빈도 단어와 복잡한 문맥에서 우수한 성능
- 다의어, 동음이의어 처리에 효과적

**Skip-gram 단점**:
- CBOW보다 학습 시간이 더 오래 걸림
- 더 많은 계산 리소스 필요
- 고빈도 단어에 대해서는 과도한 업데이트 발생 가능

**CBOW 장점**:
- **의미 깊이가 얕은 단어들**의 안정적 표현 학습
- 빠른 학습 속도와 효율적인 메모리 사용
- 고빈도 단어에 대해 robust한 임베딩 생성
- 작은 데이터셋에서 평균화 효과로 노이즈 감소

**CBOW 단점**:
- 주변 단어들의 평균화로 인한 문맥 정보 희석
- 동일 단어의 서로 다른 의미 구분 능력 제한
- 복잡한 문맥 의존적 의미 처리에 한계

## 🔍 주요개념

### 핵심 알고리즘 비교

- **Skip-gram**: 중심 단어 → 주변 단어들 예측. **의미 깊이가 깊은 단어들**의 문맥별 뉘앙스를 독립적으로 학습
- **CBOW**: 주변 단어들 → 중심 단어 예측. **의미 깊이가 얕은 단어들**의 안정적 의미를 평균화로 효율적 학습

### 선택 기준

**CBOW 선택 조건:**
- 단어들이 문맥과 관계없이 일관된 의미 사용
- 고빈도 기능어(the, and, is) 중심 처리
- 리소스 제약 환경에서 빠른 학습 필요
- 도메인 특화된 표준화된 어휘

**Skip-gram 선택 조건:**
- 문맥에 따라 의미가 크게 달라지는 다의어 많음
- 복잡하고 다층적인 의미 구조 처리 필요
- 충분한 컴퓨팅 리소스로 고품질 임베딩 요구
- 문맥적 뉘앙스가 중요한 태스크

### 실전 적용

- **복잡한 도메인 (Skip-gram 적합)**: 소셜미디어 텍스트, 문학 작품, 법률 문서에서 문맥 의존적 다의어 처리
- **단순한 도메인 (CBOW 적합)**: 기업 내부 문서, 기술 매뉴얼, 뉴스 기사에서 표준화된 용어의 효율적 임베딩
- **하이브리드 접근**: 도메인 특성에 따라 Skip-gram으로 핵심 키워드, CBOW로 일반 어휘 처리

## 📝 관련 문제

**Question:** An ML expert needs to extract embedding vectors from text sequences containing identical terms in distinct situations, including questions and answers that must be distinguished. The embedding space should capture word context and sequential information. Which solutions can generate the necessary embedding vectors?

**Options:**

- A) Amazon SageMaker seq2seq algorithm
- B) Amazon SageMaker BlazingText algorithm in Skip-gram mode
- C) Amazon SageMaker Object2Vec algorithm  
- D) Amazon SageMaker BlazingText algorithm in CBOW mode
- E) Combination of BlazingText Batch Skip-gram with custom RNN

**정답: B, C) Skip-gram mode BlazingText + Object2Vec**

💡 추가 설명:

- **Option A** - seq2seq는 번역/요약 태스크용으로 범용 임베딩 생성에는 부적합
- **Option D** - CBOW는 문맥 평균화로 인해 동일 단어의 상황별 구분 능력 제한  
- **Option E** - 과도하게 복잡한 구조로 구현 복잡도 대비 효과 불분명