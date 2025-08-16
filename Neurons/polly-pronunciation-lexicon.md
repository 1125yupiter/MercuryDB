---

title: polly-pronunciation-lexicon
created: 2025-08-16
modified: 2025-08-16
tags:
- service/polly
- problem/speech-synthesis
- technique/pronunciation-lexicon
- usecase/voice-response
- constraint/business-terms
aliases: ["Amazon Polly", "pronunciation-lexicon", "TTS", "speech-synthesis"]

---

# Amazon Polly 발음 렉시콘을 활용한 비즈니스 용어 음성 합성 최적화

## 🎯 핵심 포인트

BPO 회사에서 Amazon Polly로 음성 응답 시스템을 구축한 경우, 비즈니스 전문 용어나 약어의 발음 오류는 pronunciation lexicons를 통해 해결할 수 있다.

## 📝 설명

### Amazon Polly Pronunciation Lexicons가 비즈니스 용어 발음에 적합한 이유

Amazon Polly의 pronunciation lexicons는 특정 단어나 구문에 대한 맞춤형 발음을 정의할 수 있는 XML 기반 시스템입니다. 이는 표준 텍스트-음성 변환 엔진이 정확히 처리하지 못하는 다음과 같은 경우에 특히 유용합니다:

- **비즈니스 전문 용어**: 회사명, 제품명, 브랜드명
- **약어 및 줄임말**: BPO, API, AWS, ROI 등
- **숫자가 포함된 단어**: g3t sm4rt → "get smart"
- **외국어 단어**: 특정 언어에서 정확한 발음이 필요한 경우

렉시콘은 AWS 지역별로 저장되며, 한 번 업로드하면 해당 지역의 모든 Polly 작업에서 자동으로 적용됩니다.

### 아키텍처 플로우

```
비즈니스 텍스트 → Pronunciation Lexicon XML → Amazon Polly → 정확한 음성 출력
     ↓                    ↓                      ↓               ↓
  "BPO 서비스"         "비 피 오 서비스"        TTS 엔진      "비 피 오 서비스"
  "API 호출"           "에이 피 아이 호출"      음성 합성     "에이 피 아이 호출"
  "W3C 표준"           "월드 와이드 웹         발음 적용     "월드 와이드 웹
                        컨소시엄 표준"                        컨소시엄 표준"
```

### Trade-offs 고려사항

**Pronunciation Lexicons 장점**:
- 정확한 발음 제어 가능
- 지역별 일관된 적용
- XML 기반의 체계적 관리
- 음성학적 표기법 지원
- 대량의 전문 용어 일괄 처리

**Pronunciation Lexicons 단점**:
- 초기 설정 및 XML 파일 작성 필요
- 지역별 개별 관리 필요
- 용어 변경 시 렉시콘 업데이트 필요

**SSML pronunciation tag 장점**:
- 실시간 발음 조정 가능

**SSML pronunciation tag 단점**:
- Amazon Polly에서 지원하지 않음
- 텍스트마다 개별 처리 필요

**SSML emphasis tag 장점**:
- 강조 효과 제공

**SSML emphasis tag 단점**:
- 발음 자체를 변경하지 못함
- 음량과 속도만 조절

**Viseme Speech Mark 장점**:
- 립싱크 동기화 지원

**Viseme Speech Mark 단점**:
- 발음 교정과 무관
- 시각적 동기화 목적만

## 🔍 주요개념

### 핵심 개념 비교

- **Pronunciation Lexicons**: XML 파일을 통해 특정 단어의 발음을 사전에 정의하는 방식
- **SSML (Speech Synthesis Markup Language)**: 음성 합성을 위한 마크업 언어로 실시간 조정 가능
- **Viseme Speech Marks**: 입술 움직임과 음성을 동기화하기 위한 메타데이터
- **Phonetic Alphabet**: 국제음성기호(IPA) 등을 사용한 정확한 발음 표기

### 실전 적용

- **고객 서비스 센터**: "FAQ", "SLA", "KPI" 등 비즈니스 약어의 정확한 발음
- **금융 서비스**: "ROI", "B2B", "FinTech" 등 금융 전문 용어 처리
- **기술 지원**: "API", "SDK", "IoT" 등 기술 용어의 명확한 음성 안내

## 📝 관련 문제

**Question:** A Business Process Outsourcing (BPO) company uses Amazon Polly to translate plaintext documents to speech for its voice response system. After testing, some acronyms and business-specific terms are being pronounced incorrectly. Which approach will fix this issue?

**Options:**

- A) Convert the scripts into Speech Synthesis Markup Language (SSML) and use the pronunciation tag
- B) Use a viseme Speech Mark
- C) Use pronunciation lexicons
- D) Convert the scripts into Speech Synthesis Markup Language (SSML) and use the emphasis tag to guide the pronunciation

**정답: C) Use pronunciation lexicons**

💡 추가 설명:

- **Option A** - Amazon Polly에서 SSML pronunciation tag를 지원하지 않음
- **Option B** - Viseme Speech Mark는 립싱크용으로 발음 교정과 무관
- **Option D** - SSML emphasis tag는 강조 효과만 제공하며 발음 자체를 변경하지 않음