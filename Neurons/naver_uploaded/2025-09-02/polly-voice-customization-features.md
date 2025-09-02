---

title: polly-voice-customization-features
created: 2025-08-21
modified: 2025-08-21
tags:
- service/polly
- feature/voice-customization
- capability/speech-synthesis
- customization/pronunciation
- ai/text-to-speech
aliases: ["polly-custom", "voice-custom", "tts-custom"]

---

# AWS Polly 음성 커스터마이징 - 자연스러운 음성 합성을 위한 세 가지 핵심 기능

## 🎯 핵심 포인트

Amazon Polly에서 합성된 음성의 목소리와 발음을 커스터마이징하는 경우 Speech marks, Prosody, Lexicons의 세 가지 기능을 모두 활용할 수 있다.

## 📝 설명

### Amazon Polly가 고품질 TTS 서비스에 적합한 이유

Amazon Polly는 딥러닝 기술을 활용하여 자연스러운 인간 목소리와 같은 음성을 합성하는 텍스트-음성 변환(TTS) 서비스입니다. 단순한 음성 변환을 넘어서 세밀한 커스터마이징이 가능하여 다양한 비즈니스 시나리오에서 활용됩니다.

### 아키텍처 플로우

```
Input Text → [Lexicons 적용] → [Speech Marks 처리] → [Prosody 조정] → Synthesized Audio Output
     ↓              ↓                  ↓                   ↓
   원본 텍스트    발음 사전 적용      타이밍/강조 제어      운율/음조 조정
```

### 핵심 커스터마이징 기능

**1. Speech Marks (음성 마크)**
- XML 태그를 통한 세밀한 음성 제어
- 개별 단어/구문의 타이밍과 발음 조정
- 강조, 억양 변경, 일시정지, 호흡음 삽입 가능
- SSML(Speech Synthesis Markup Language) 표준 지원

**2. Prosody (운율)**
- 음성의 속도(rate), 음량(volume), 음높이(pitch) 제어
- 자연스럽고 표현력 있는 음성 생성
- 감정과 의도를 반영한 억양 조절
- 단어별/구문별 세밀한 강조 가능

**3. Lexicons (어휘집)**
- 사용자 정의 발음 사전 생성
- 전문 용어, 브랜드명, 개인 이름의 정확한 발음
- 도메인별 특수 용어 학습
- 다국어 발음 규칙 적용

### Trade-offs 고려사항

**Amazon Polly 장점**:
- 높은 음질과 자연스러운 발음
- 다양한 언어와 음성 지원
- 실시간 음성 합성
- 세밀한 커스터마이징 옵션

**Amazon Polly 단점**:
- 사용량 기반 요금제로 대량 처리 시 비용 부담
- 복잡한 SSML 태그 학습 필요
- 실시간 처리 시 레이턴시 고려 필요

**대안 서비스(Google TTS, Azure Cognitive Services) 장점**:
- 특정 언어에서 더 나은 음질
- 다른 클라우드 생태계와의 통합

**대안 서비스 단점**:
- AWS 서비스와의 통합성 부족
- 커스터마이징 옵션의 제한
- 일관된 음질 보장의 어려움

## 🔍 주요개념

### 커스터마이징 기능 비교

- **Speech Marks**: 시간 기반 제어와 강조 표현에 특화된 XML 태그 시스템
- **Prosody**: 음성의 물리적 특성(속도, 음량, 음높이) 조절에 특화
- **Lexicons**: 발음 정확성과 전문 용어 처리에 특화된 사전 시스템

### 실전 적용

- **고객 서비스 챗봇**: 브랜드 이름과 전문 용어의 정확한 발음으로 전문성 향상
- **교육 플랫폼**: 수학 공식이나 과학 용어의 명확한 발음으로 학습 효과 증대
- **오디오북 제작**: 등장인물별 목소리 변화와 감정 표현으로 몰입감 향상

## 📝 관련 문제

**Question:** Which of the following AWS Polly features allow you to customize the voice and pronunciation of synthesized speech?

**Options:**

- A) Speech marks
- B) Prosody
- C) Lexicons
- D) None of the above
- E) All of the above

**정답: E) All of the above**

💡 추가 설명:

- **Speech marks** - XML 태그를 사용해 개별 단어나 구문의 타이밍과 발음을 제어하며, 강조나 일시정지 등을 추가할 수 있음
- **Prosody** - 합성된 음성의 속도, 음량, 음높이를 제어하여 더 자연스럽고 표현력 있는 음성을 만들 수 있음
- **Lexicons** - 사용자 정의 발음 사전을 생성하여 전문 용어나 브랜드명의 정확한 발음을 학습시킬 수 있음
- **None of the above** - 위의 모든 기능이 음성 커스터마이징에 사용되므로 부적합
