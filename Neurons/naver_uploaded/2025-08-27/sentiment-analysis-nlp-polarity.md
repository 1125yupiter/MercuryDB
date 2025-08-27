---

title: sentiment-analysis-nlp-polarity
created: 2025-08-19
modified: 2025-08-19
tags:
- technique/sentiment-analysis
- domain/nlp
- method/polarity-scoring
- application/text-mining
- usecase/social-monitoring
aliases: ["sentiment", "polarity", "emotion-detection"]

---

# 감정 분석을 통한 텍스트 데이터 극성 값 할당 기술

## 🎯 핵심 포인트

텍스트 데이터의 감정적 톤을 분석해야 하는 경우 NLP에서, 각 단어에 극성 값을 할당하는 감정 분석(Sentiment Analysis) 기술을 활용할 수 있다.

## 📝 설명

### 감정 분석이 텍스트 극성 분석에 적합한 이유

감정 분석은 텍스트 내 각 단어의 감정적 의미를 수치화하여 전체 텍스트의 감정적 톤을 파악하는 NLP 기술입니다. 단어별로 긍정적(positive), 부정적(negative), 또는 중립적(neutral) 극성 값을 할당하고, 문맥적 의미를 고려하여 가중치를 부여합니다. 이를 통해 소셜 미디어 모니터링, 고객 피드백 분석, 브랜드 평판 관리 등 다양한 비즈니스 시나리오에서 활용됩니다.

### 아키텍처 플로우

```
Raw Text → Preprocessing → Tokenization → Word-level Polarity Assignment 
    ↓                                                    ↓
Result Analysis ← Sentiment Aggregation ← Context Analysis ← Feature Extraction
```

### Trade-offs 고려사항

**감정 분석 장점**:
- 단어별 세밀한 극성 값 할당으로 정확한 감정 파악
- 대용량 텍스트 데이터의 자동화된 감정 분석
- 실시간 소셜 미디어 모니터링 가능
- 문맥적 의미를 고려한 가중치 부여

**감정 분석 단점**:
- 언어별, 도메인별 사전 구축 필요
- 복잡한 문맥이나 반어법 처리의 한계
- 문화적 뉘앙스나 슬랭 처리 어려움

**다른 NLP 기술들과의 차이점**:
- **개체명 인식(NER)**: 감정이 아닌 개체 식별에 특화
- **품사 태깅(POS)**: 문법적 분류만 수행, 감정 분석 불가
- **토픽 모델링**: 주제 발견에 집중, 감정적 극성 분석 없음

## 🔍 주요개념

### 핵심 개념 비교

- **극성 값(Polarity Value)**: 각 단어나 구문에 할당되는 감정적 가중치 (-1~+1 범위)
- **감정 사전(Sentiment Lexicon)**: 단어별 극성 값이 미리 정의된 데이터베이스
- **문맥 분석(Context Analysis)**: 주변 단어들의 관계를 고려한 감정 해석
- **감정 집계(Sentiment Aggregation)**: 개별 단어 극성을 종합하여 전체 감정 도출

### 실전 적용

- **소셜 미디어 모니터링**: 브랜드 언급에 대한 실시간 감정 분석
- **고객 피드백 분석**: 제품 리뷰나 설문 응답의 자동 감정 분류
- **시장 조사**: 경쟁사 대비 브랜드 평판 비교 분석
- **콘텐츠 추천**: 사용자 선호도 기반 개인화된 콘텐츠 제공

## 📝 관련 문제

**Question:** A company wants to analyze customer reviews to understand the emotional tone of feedback. They need to assign sentiment scores to individual words in the text to determine overall customer satisfaction. Which NLP technique should they implement?

**Options:**

- A) Named Entity Recognition (NER)
- B) Part-of-Speech Tagging (POS)
- C) Topic Modeling
- D) Sentiment Analysis
- E) Text Summarization

**정답: D) Sentiment Analysis**

💡 추가 설명:

- **Named Entity Recognition (NER)** - 사람명, 조직명 등 개체 식별용으로 감정 분석 불가
- **Part-of-Speech Tagging (POS)** - 품사 분류만 수행하며 감정적 극성 할당 기능 없음
- **Topic Modeling** - 문서 집합의 잠재 주제 발견용으로 단어별 감정 분석 불가
- **Text Summarization** - 텍스트 요약 기술로 감정 극성 분석과는 목적이 다름