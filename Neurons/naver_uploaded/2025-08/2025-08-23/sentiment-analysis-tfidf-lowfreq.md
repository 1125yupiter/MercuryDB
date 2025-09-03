---

title: sentiment-analysis-tfidf-lowfreq
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/sentiment-analysis
- technique/tfidf-vectorization
- constraint/low-frequency-words
- service/scikit-learn
- method/text-preprocessing
aliases: ["tfidf-sentiment", "text-vectorization", "word-frequency"]

---

# TF-IDF를 활용한 감정 분석에서 저빈도 단어 문제 해결

## 🎯 핵심 포인트

풍부한 어휘와 낮은 평균 빈도 단어로 인해 검증 정확도가 불만족스러운 경우 감정 분석에서, Scikit-learn의 TfidfVectorizer를 사용하여 단어의 상대적 중요도를 고려한 벡터화를 수행할 수 있다.

## 📝 설명

### TF-IDF가 감정 분석의 저빈도 단어 문제에 적합한 이유

TF-IDF(Term Frequency-Inverse Document Frequency)는 텍스트를 수치적 특성으로 변환하는 기법으로, 단순한 단어 빈도가 아닌 상대적 중요도를 계산합니다. Term Frequency는 특정 문서 내에서 단어가 얼마나 자주 나타나는지를 측정하고, Inverse Document Frequency는 전체 문서 집합에서 해당 단어가 얼마나 희귀한지를 측정합니다.

특히 풍부한 어휘와 많은 저빈도 단어가 있는 데이터셋에서 TF-IDF는 너무 일반적인 단어의 가중치를 줄이면서 더 특별한 단어들을 강조하여 모델 성능과 검증 정확도를 개선합니다.

### 아키텍처 플로우

```
원시 텍스트 데이터
    ↓
TF 계산 (문서별 단어 빈도)
    ↓
IDF 계산 (전체 말뭉치에서 단어 희귀성)
    ↓
TF × IDF = 최종 가중치
    ↓
수치화된 특성 벡터
    ↓
머신러닝 모델 학습
    ↓
개선된 검증 정확도
```

### Trade-offs 고려사항

**TfidfVectorizer 장점**:
- 단어의 상대적 중요도를 반영한 벡터화
- 일반적인 단어(불용어 등)의 영향 자동 감소
- 희귀하지만 중요한 단어의 가중치 증가
- 고차원 희소 데이터에서도 효과적 성능

**TfidfVectorizer 단점**:
- CountVectorizer보다 계산 복잡도 높음
- 메모리 사용량 증가 가능
- 하이퍼파라미터 튜닝 필요

**CountVectorizer 장점**:
- 단순한 구현과 빠른 처리
- 해석하기 쉬운 결과

**CountVectorizer 단점**:
- 단순 빈도만 고려하여 단어 중요도 무시
- 저빈도 단어 문제 해결 불가

## 🔍 주요개념

### 핵심 개념 비교

- **TF (Term Frequency)**: 특정 문서에서 단어가 나타나는 빈도를 해당 문서의 전체 단어 수로 나눈 값
- **IDF (Inverse Document Frequency)**: 전체 문서 수를 해당 단어를 포함한 문서 수로 나눈 후 로그를 취한 값
- **TF-IDF Score**: TF × IDF로 계산되며, 특정 문서에서는 빈번하지만 전체 말뭉치에서는 희귀한 단어에 높은 점수 부여

### 실전 적용

- **제품 리뷰 감정 분석**: "배송", "포장"과 같은 일반적 단어보다 "혁신적", "실망"과 같은 감정 표현 단어에 높은 가중치 부여
- **소셜 미디어 감정 분석**: 해시태그나 특정 브랜드명과 같은 고유한 단어들의 중요도 강조
- **고객 피드백 분류**: 업계별 전문 용어나 특정 불만 표현에 적절한 가중치 할당

## 📝 관련 문제

**Question:** A Machine Learning Specialist is building a project that runs sentiment analysis for product reviews. Because the validation accuracy is not satisfactory, the Specialist believes that a rich vocabulary plus a low average frequency of words in the training data is causing the issue. How can the Specialist improve the validation accuracy of the model?

**Options:**

- A) Use the Scikit-learn TfidfVectorizer Class
- B) Use the Scikit-learn CountVectorizer Class
- C) Apply Stemming and remove stop words using Python's Natural Language Toolkit (NLTK) Library
- D) Create a custom entity with Amazon Comprehend

**정답: A) Use the Scikit-learn TfidfVectorizer Class**

💡 추가 설명:

- **CountVectorizer** - 단순히 단어 출현 횟수만 계산하여 단어의 상대적 중요도를 고려하지 못함
- **NLTK Stemming/Stop words** - 어휘 크기를 줄이지만 저빈도 단어 가중치 문제를 직접적으로 해결하지 못함
- **Amazon Comprehend Custom Entity** - 명명된 개체 인식용 기능으로 단어 가중치 문제와는 무관함