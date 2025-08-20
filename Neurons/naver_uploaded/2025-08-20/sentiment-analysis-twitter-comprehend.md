---

title: sentiment-analysis-twitter-comprehend
created: 2025-08-17
modified: 2025-08-17
tags:
- service/comprehend
- problem/sentiment-analysis
- usecase/social-media
- constraint/real-time
- platform/twitter
aliases: ["twitter-sentiment", "social-sentiment-analysis", "comprehend-sentiment"]

---

# Twitter 감정 분석을 위한 Amazon Comprehend 기반 스마트 웹 크롤러

## 🎯 핵심 포인트

트위터 텍스트의 감정을 실시간으로 분석하고 메타데이터를 인덱싱해야 하는 경우 Amazon OpenSearch와 함께, Amazon Comprehend를 사용할 수 있다.

## 📝 설명

### Amazon Comprehend가 트위터 감정 분석에 적합한 이유

Amazon Comprehend는 기계학습을 사용하여 비정형 텍스트 데이터의 인사이트와 관계를 발견하는 자연어 처리 서비스입니다. 트위터와 같은 소셜 미디어 텍스트에 대해 언어 감지, 핵심 구문 추출, 개체명 인식, 감정 분석을 수행할 수 있으며, 특히 `detect_sentiment` API를 통해 텍스트의 긍정적/부정적 정도를 Positive, Negative, Neutral, Mixed 카테고리로 분류하고 각각의 신뢰도 점수를 제공합니다.

### 아키텍처 플로우

```
Twitter API → Lambda (크롤링) → SQS (큐잉) → Lambda (처리)
                                                    ↓
EventBridge (스케줄링) ← OpenSearch (인덱싱) ← Comprehend (감정분석)
                                                    ↓
                            API Gateway ← Lambda (비즈니스 로직)
```

### Trade-offs 고려사항

**Amazon Comprehend 장점**:
- 사전 훈련된 ML 모델로 즉시 사용 가능
- 다국어 지원 (한국어, 영어 등)
- 실시간 API 호출로 낮은 지연시간
- AutoML 기능으로 커스텀 모델 구축 가능
- AWS 생태계와의 완벽한 통합

**Amazon Comprehend 단점**:
- API 호출당 과금으로 대용량 처리 시 비용 증가
- 도메인 특화 감정 분석에는 추가 튜닝 필요
- 텍스트 길이 제한 (5,000 바이트)

**Amazon Rekognition 장점**:
- 이미지/비디오 분석에 특화
- 얼굴 감정 인식 가능

**Amazon Rekognition 단점**:
- 텍스트 감정 분석 불가능
- 트위터 텍스트 처리에 부적합

**Amazon Transcribe 장점**:
- 음성을 텍스트로 변환
- 실시간 스트리밍 지원

**Amazon Transcribe 단점**:
- 텍스트 감정 분석 기능 없음
- 이미 텍스트인 트윗에는 불필요

**Amazon Lex 장점**:
- 대화형 인터페이스 구축
- 의도 파악 가능

**Amazon Lex 단점**:
- 챗봇 구축에 특화되어 일반적인 감정 분석에는 제한적
- 배치 처리 지원 부족

## 🔍 주요개념

### 감정 분석 유형 비교

- **실시간 감정 분석**: API 호출을 통한 즉시 처리, 낮은 지연시간, 트위터 실시간 모니터링에 적합
- **배치 감정 분석**: 대용량 텍스트 파일 처리, 비용 효율적, 과거 데이터 분석에 적합

### 실전 적용

- **브랜드 모니터링**: 제품/서비스에 대한 실시간 소비자 반응 추적
- **위기 관리**: 부정적 감정 급증 시 즉시 알림 및 대응
- **마케팅 캠페인 효과 측정**: 캠페인 전후 감정 변화 분석
- **고객 서비스 개선**: 불만 트윗 자동 감지 및 우선순위 처리
- **주식/암호화폐 시장 분석**: 투자자 심리 분석을 통한 시장 예측

## 📝 관련 문제

**Question:** A machine learning expert is building a smart web crawler to analyze tweets using sentiment analysis. The goal is to index the scraped tweets with sentiment metadata in Amazon OpenSearch for fast data searches. Which service should be used for this scenario?

**Options:**

- A) Amazon Rekognition
- B) Amazon Transcribe  
- C) Amazon Comprehend
- D) Amazon Lex
- E) Amazon Textract

**정답: C) Amazon Comprehend**

💡 추가 설명:

- **Amazon Rekognition** - 이미지 및 비디오 분석 전용 서비스로 텍스트 감정 분석 불가능
- **Amazon Transcribe** - 음성을 텍스트로 변환하는 서비스로 감정 분석 기능 없음
- **Amazon Lex** - 챗봇 구축에 특화되어 일반적인 감정 분석에는 기능 제한적
- **Amazon Textract** - 문서에서 텍스트 추출하는 서비스로 감정 분석 기능 없음