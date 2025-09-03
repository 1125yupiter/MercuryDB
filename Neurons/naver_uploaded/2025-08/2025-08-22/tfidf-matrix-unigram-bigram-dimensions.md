---

title: tfidf-matrix-unigram-bigram-dimensions
created: 2025-08-18
modified: 2025-08-18
tags:
- technique/tf-idf
- method/natural-language-processing
- concept/n-gram
- feature/text-processing
- problem/text-mining
aliases: ["tf-idf matrix", "text feature extraction", "nlp"]

---

# TF-IDF 매트릭스의 차원 결정 - 유니그램과 바이그램을 활용한 텍스트 특성 추출

## 🎯 핵심 포인트

텍스트 코퍼스에서 유니그램과 바이그램을 모두 사용한 경우 TF-IDF 매트릭스에서, 행은 문서 수, 열은 전체 유니크한 n-gram 피처 수로 결정할 수 있다.

## 📝 설명

### TF-IDF가 텍스트 특성 추출에 적합한 이유

TF-IDF(Term Frequency-Inverse Document Frequency)는 텍스트 마이닝에서 단어의 중요도를 수치화하는 핵심 기법입니다. 단순한 단어 빈도수가 아닌, 해당 단어가 특정 문서에서는 자주 나타나지만 전체 코퍼스에서는 드물게 나타날 때 높은 가중치를 부여합니다. 유니그램과 바이그램을 함께 사용하면 단어의 개별적 중요성과 단어 간의 연관성을 동시에 포착할 수 있습니다.

### 매트릭스 차원 계산 플로우

```
입력 코퍼스 (2개 문장)
    ↓
유니그램 추출 → 중복 제거 → 유니크 유니그램 집합 (8개)
    ↓
바이그램 추출 → 중복 제거 → 유니크 바이그램 집합 (8개)
    ↓
전체 피처 결합 → 총 16개 유니크 피처
    ↓
TF-IDF 매트릭스 (2 × 16)
```

### Trade-offs 고려사항

**유니그램 + 바이그램 조합 장점**:
- 단어의 개별적 의미와 문맥적 의미를 동시에 포착
- 더 풍부한 텍스트 표현력 제공
- 단어 순서 정보 일부 보존

**유니그램 + 바이그램 조합 단점**:
- 피처 차원 증가로 인한 계산 복잡도 상승
- 희소 매트릭스(sparse matrix) 문제 심화
- 메모리 사용량 증가

**유니그램만 사용 장점**:
- 간단한 구현과 빠른 처리 속도
- 낮은 차원의 피처 공간

**유니그램만 사용 단점**:
- 단어 순서 정보 완전 손실
- 문맥적 의미 포착 불가능

## 🔍 주요개념

### N-gram 유형별 특성

- **유니그램(Unigram)**: 개별 단어 단위로 텍스트를 분할하여 각 단어의 출현 빈도를 측정
- **바이그램(Bigram)**: 연속된 두 단어의 조합으로 단어 간의 순서와 연관성을 부분적으로 포착

### 실전 적용

- 감정 분석에서 "very good", "not bad" 같은 의미적 조합 포착
- 문서 분류에서 도메인별 전문 용어 조합 인식
- 검색 엔진에서 사용자 쿼리의 의미적 맥락 이해

## 📝 관련 문제

**Question:** A Machine Learning Specialist is using term frequency–inverse document frequency (tf–idf) to extract the keyword frequency of a corpus using unigrams and bigrams. The corpus consists of two sentences as shown below:
- "Don't stop believing in yourself"  
- "Don't quit on yourself now"

Which dimension represents this tf-idf matrix?

**Options:**

- A) (2, 8)
- B) (2, 12)
- C) (2, 16)
- D) (15, 2)
- E) (8, 2)

**정답: C) (2, 16)**

💡 추가 설명:

- **A) (2, 8)** - 유니그램만 고려한 경우의 차원으로, 바이그램 피처를 누락
- **B) (2, 12)** - 피처 수 계산이 부정확하며, 실제 유니크한 n-gram 수와 불일치
- **D) (15, 2)** - 행과 열이 바뀌어 있으며, 문서 수가 행이 되어야 함
- **E) (8, 2)** - 완전히 잘못된 차원으로 매트릭스의 기본 구조를 이해하지 못한 경우

**상세 계산:**
- 유니크 유니그램: ['believing', 'don't', 'in', 'now', 'on', 'quit', 'stop', 'yourself'] = 8개
- 유니크 바이그램: ['believing in', 'don't quit', 'don't stop', 'in yourself', 'on yourself', 'quit on', 'stop believing', 'yourself now'] = 8개
- 총 피처 수: 8 + 8 = 16개
- 매트릭스 차원: (문서 수, 피처 수) = (2, 16)