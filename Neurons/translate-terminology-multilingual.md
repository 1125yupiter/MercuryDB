---

title: translate-terminology-multilingual
created: 2025-08-21
modified: 2025-08-21
tags:
- service/translate
- feature/terminology
- capability/multilingual
- management/api-console
- engine/nmt-smt
aliases: ["aws-translate-terminology", "custom-terminologies", "translate-custom-terms"]

---

# AWS Translate Custom Terminologies의 다언어 지원과 유연한 관리

## 🎯 핵심 포인트

기업이 특정 용어의 정확한 번역을 보장하고자 하는 경우 AWS Translate의 Custom Terminologies에서, 모든 지원 언어 쌍에 대해 용어집을 생성하고 다양한 방법으로 관리할 수 있다.

## 📝 설명

### AWS Translate Custom Terminologies가 기업 번역에 적합한 이유

AWS Translate의 Custom Terminologies는 기업의 전문 용어, 브랜드명, 제품명 등을 정확하게 번역할 수 있도록 맞춤형 용어집을 제공하는 기능입니다. 의료, 법률, 기술 등 전문 분야뿐만 아니라 모든 도메인에서 활용 가능하며, 기계번역의 정확성을 크게 향상시킵니다.

### 아키텍처 플로우

```
용어집 생성 (CSV/TSV 파일) 
    ↓
AWS Translate에 업로드 (Console/API/CLI/SDK)
    ↓
번역 작업 시 용어집 지정
    ↓
SMT/NMT 엔진에서 용어집 적용
    ↓
정확한 맞춤형 번역 결과 출력
```

### Trade-offs 고려사항

**Custom Terminologies 장점**:
- 모든 지원 언어 쌍에 적용 가능한 범용성
- Console, API, CLI, SDK 등 다양한 관리 방법 제공
- SMT와 NMT 엔진 모두에서 사용 가능
- 도메인 제한 없이 모든 분야에서 활용 가능
- 브랜드명, 제품명 등 고유 용어의 일관된 번역 보장

**Custom Terminologies 단점**:
- 초기 용어집 구축 및 유지보수 필요
- 용어집 크기에 따른 번역 속도 영향 가능성
- 언어별 용어집 품질에 따른 번역 결과 차이

**기본 번역 서비스 장점**:
- 별도 설정 없이 즉시 사용 가능
- 빠른 번역 속도

**기본 번역 서비스 단점**:
- 전문 용어나 고유명사 번역의 부정확성
- 일관되지 않은 용어 번역 결과

## 🔍 주요개념

### 핵심 개념 비교

- **Custom Terminologies**: 사용자 정의 용어집을 통해 특정 용어의 번역을 강제로 지정하는 기능
- **Neural Machine Translation (NMT)**: 신경망 기반 기계번역 엔진으로 Custom Terminologies 지원
- **Statistical Machine Translation (SMT)**: 통계 기반 기계번역 엔진으로 Custom Terminologies 지원

### 실전 적용

- 글로벌 기업의 제품 매뉴얼 번역 시 제품명과 기술 용어의 일관된 번역
- 의료기기 회사의 다국어 문서에서 의료 용어의 정확한 번역
- 법률 문서 번역 시 법률 용어의 표준화된 번역 적용

## 📝 관련 문제

**Question:** Which of the following statements is true regarding Amazon Translate's support for custom terminologies in machine translation?

**Options:**

- A) Custom terminologies can only be created and managed using the Amazon Translate console.
- B) Custom terminologies can be created in any language and can be applied to translation from and to any language pair.
- C) Custom terminologies can only be used to translate text in specific domains, such as medical or legal.
- D) Custom terminologies are only available for the Amazon Translate neural machine translation (NMT) engine.

**정답: B) Custom terminologies can be created in any language and can be applied to translation from and to any language pair.**

💡 추가 설명:

- **Option A** - Console 외에도 API, CLI, SDK 등 다양한 방법으로 생성 및 관리 가능
- **Option C** - 특정 도메인에만 제한되지 않고 모든 분야에서 활용 가능
- **Option D** - NMT뿐만 아니라 SMT 엔진에서도 사용 가능