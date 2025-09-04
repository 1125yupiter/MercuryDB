---
title: asr-transcribe-custom-vocabulary
created: 2025-08-23
modified: 2025-08-23
tags:
- service/transcribe
- problem/speech-recognition
- constraint/custom-vocabulary
- usecase/product-identification
- technique/phonetic-pronunciation
aliases: ["ASR customization", "speech transcription", "custom vocabulary"]

---

# Amazon Transcribe가 특정 제품명 인식에 적합한 이유

## 🎯 핵심 포인트

음성인식에서 특정 제품명의 정확한 인식이 필요한 경우 Amazon Transcribe에서, Custom Vocabulary 파일과 발음 표기를 활용하여 높은 정확도를 달성할 수 있다.

## 📝 설명

### Amazon Transcribe가 제품명 인식에 적합한 이유

Amazon Transcribe는 사전 훈련된 음성인식 모델을 제공하며, Custom Vocabulary 기능을 통해 특정 도메인의 용어나 제품명에 대한 인식 정확도를 크게 향상시킬 수 있다. 특히 독특한 철자나 발음을 가진 200개의 제품명을 정확히 인식해야 하는 상황에서, 발음 표기(phonetic pronunciation)를 포함한 사용자 정의 어휘 파일을 구성하면 최적의 결과를 얻을 수 있다.

### 아키텍처 플로우

```
음성 파일 입력 
    ↓
Amazon Transcribe 서비스
    ↓
Custom Vocabulary 파일 참조
    ↓
제품명 발음 매칭
    ↓
텍스트 전사 결과 출력
    ↓
정확도 분석 및 어휘 파일 업데이트
```

### Trade-offs 고려사항

**Amazon Transcribe 장점**:
- 사전 훈련된 고성능 ASR 모델 제공
- Custom Vocabulary를 통한 특정 용어 최적화 가능
- 발음 표기를 통한 정확도 향상
- 실시간 및 배치 처리 모두 지원
- 시간당 여러 번의 수정 가능한 유연성

**Amazon Transcribe 단점**:
- Custom Language Model 대비 상대적으로 제한적인 맞춤화
- 발음 표기 작성에 전문 지식 필요
- 초기 어휘 파일 구성에 시간 투자 필요

**Amazon Lex 장점**:
- 대화형 인터페이스에 특화
- 슬롯과 동의어 메커니즘 제공

**Amazon Lex 단점**:
- ASR보다는 대화 이해에 초점
- 단순 전사 작업에는 과도한 복잡성
- 음성인식 정확도에서 Transcribe 대비 제한적

## 🔍 주요개념

### Custom Vocabulary vs Custom Language Model

- **Custom Vocabulary**: 특정 단어나 구문의 인식을 향상시키기 위해 발음 표기와 함께 어휘 목록을 제공하는 방식
- **Custom Language Model**: 대량의 텍스트 데이터를 사용하여 언어 모델 자체를 재훈련하는 방식

### 실전 적용

- 의료 분야에서 약물명이나 의학 용어의 정확한 전사
- 소매업체에서 고유 브랜드명이나 제품명 인식
- 콜센터에서 회사별 전문 용어나 서비스명 처리

## 📝 관련 문제

**Question:** An organization is implementing automatic speech recognition (ASR) for a voicemail application to transcribe messages under 60 seconds. They need accurate identification of 200 distinct product names with unique spellings and pronunciations. The company has 4,000 words of Amazon SageMaker Ground Truth voicemail transcripts for customization and requires the ability to make multiple modifications per hour. Which approach will provide the highest transcription accuracy during development?

**Options:**

- A) Use Amazon Lex bot for ASR customization with custom slots for product names and synonym mechanisms
- B) Use Amazon Transcribe with automatic custom vocabulary updates based on confidence scores below threshold
- C) Create custom vocabulary file with phonetic pronunciations for Amazon Transcribe and manually update based on transcript analysis
- D) Build Amazon Transcribe custom language model using audio transcripts as training dataset with manual corrections
- E) Implement hybrid approach combining multiple AWS services

**정답: C) Create custom vocabulary file with phonetic pronunciations for Amazon Transcribe**

💡 추가 설명:

- **Option A** - Amazon Lex는 대화형 봇 구축에 특화되어 있어 단순 ASR 전사 작업에는 부적합하며, 음성인식 정확도에서 Transcribe보다 제한적임
- **Option B** - 신뢰도 점수 기반 자동 업데이트는 잘못된 전사를 어휘에 추가할 위험이 있고, 발음 정보 없이는 정확도 향상이 제한적임
- **Option D** - Custom Language Model 구축은 4,000단어로는 부족하며, 200개 제품명에 특화된 맞춤화에는 Custom Vocabulary가 더 효과적임
- **Option E** - 하이브리드 접근법은 복잡성만 증가시키고 단일 서비스 최적화가 더 효율적임

---