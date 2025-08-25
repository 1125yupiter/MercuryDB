---

title: topic-modeling-comprehend-sagemaker-unsupervised
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/topic-modeling
- service/comprehend
- service/sagemaker
- method/unsupervised
- usecase/document-classification
aliases: ["LDA", "NTM", "topic-discovery"]

---

# AWS 주제 모델링 - 백과사전 자동 분류를 위한 비지도 학습 솔루션

## 🎯 핵심 포인트

라벨이 없는 대량의 텍스트 문서를 자동 분류해야 하는 경우, Amazon Comprehend LDA와 SageMaker NTM에서 비지도 학습 기반 주제 모델링을 활용할 수 있다.

## 📝 설명

### 비지도 주제 모델링이 대량 문서 분류에 적합한 이유

백과사전과 같은 대량의 텍스트 데이터를 분류할 때 가장 큰 도전은 기존 라벨이 없다는 것입니다. 비지도 학습 기반의 주제 모델링은 텍스트 내의 숨겨진 패턴과 단어 분포를 분석하여 자동으로 주제를 발견하고 문서를 분류할 수 있습니다.

**Amazon Comprehend LDA**는 전통적인 확률론적 주제 모델링 알고리즘으로, 문서-주제 분포와 주제-단어 분포를 동시에 학습합니다. 각 문서가 여러 주제의 혼합으로 구성된다고 가정하여, 실제 백과사전 기사의 복잡한 내용 구조를 잘 반영합니다.

**SageMaker NTM**은 신경망 기반의 더 고급 주제 모델링 알고리즘으로, 전통적인 LDA보다 복잡한 주제 간 관계를 포착할 수 있습니다. 특히 대규모 데이터셋에서 더 나은 성능을 보이며, 주제의 계층적 구조도 학습할 수 있습니다.

### 아키텍처 플로우

```
원본 텍스트 문서
        ↓
    전처리 단계
    (토큰화, 불용어 제거)
        ↓
┌─────────────────┐    ┌─────────────────┐
│  Comprehend LDA │    │ SageMaker NTM   │
│                 │    │                 │
│ • 확률론적 모델     │    │ • 신경망 기반     │
│ • 해석 용이        │    │ • 복잡한 패턴     │
│ • 빠른 처리       │     │ • 대규모 최적화.  │
└─────────────────┘    └─────────────────┘
        ↓                      ↓
주제별 문서 분류 결과
        ↓
검색/조회 시스템 연동
```

### Trade-offs 고려사항

**Amazon Comprehend LDA 장점**:
- 완전 관리형 서비스로 인프라 관리 불필요
- 결과 해석이 상대적으로 용이
- 중소 규모 데이터셋에서 빠른 처리
- 확률론적 접근으로 불확실성 정보 제공

**Amazon Comprehend LDA 단점**:
- 복잡한 주제 관계 포착 한계
- 커스터마이징 옵션 제한적
- 매우 큰 데이터셋에서 성능 한계

**SageMaker NTM 장점**:
- 복잡한 주제 패턴과 계층 구조 학습 가능
- 대규모 데이터셋에서 우수한 성능
- 하이퍼파라미터 튜닝을 통한 최적화 가능
- 분산 처리로 확장성 우수

**SageMaker NTM 단점**:
- 더 많은 설정과 튜닝 필요
- 결과 해석이 상대적으로 복잡
- 인프라 관리 및 비용 고려 필요

## 🔍 주요개념

### 핵심 개념 비교

- **LDA (Latent Dirichlet Allocation)**: 각 문서를 여러 주제의 확률적 혼합으로 모델링하는 전통적 주제 모델링 알고리즘
- **NTM (Neural Topic Model)**: 신경망을 활용하여 더 복잡한 주제 구조와 단어 관계를 학습하는 현대적 접근법
- **비지도 학습**: 라벨 없는 데이터에서 패턴을 자동 발견하는 기계학습 방법론

### 실전 적용

- **디지털 도서관**: 수만 권의 도서를 주제별로 자동 분류하여 검색 효율성 향상
- **뉴스 아카이브**: 과거 기사들을 시대별, 주제별로 자동 정리하여 연구 지원
- **기업 지식 베이스**: 내부 문서들을 부서별, 프로젝트별 주제로 자동 분류하여 지식 관리 효율화

## 📝 관련 문제

**Question:** As part of a major content digitization initiative, your team has been tasked with organizing a vast library of encyclopedia articles to enable efficient search and retrieval. The articles are currently stored in raw text format, without any pre-assigned topic labels or categories. To unlock the full value of this content, you need a way to automatically classify the articles into relevant topics with minimal manual effort. Which AWS services or tools would you recommend using to tackle this challenge? (SELECT TWO)

**Options:**

- A) Use Amazon Comprehend's Latent Dirichlet Allocation (LDA) feature to automatically classify the encyclopedia articles.
- B) Use Amazon SageMaker Ground Truth to manually label a sample of the articles, then train a custom text classification model on the labeled data.
- C) Leverage Amazon Textract's document understanding capabilities to extract topics from the article text.
- D) Employ Amazon Kendra for natural language search, allowing users to find articles by querying with natural language without pre-classifying them into topics.
- E) Use the Amazon SageMaker Neural Topic Model (NTM) to automatically discover and assign topics to the encyclopedia articles.

**정답: A) Amazon Comprehend LDA, E) SageMaker NTM**

💡 추가 설명:

- **Ground Truth (옵션 B)** - 수동 라벨링이 필요하여 "최소한의 수작업"이라는 요구사항에 부합하지 않음
- **Textract (옵션 C)** - 스캔된 문서에서 텍스트 추출용 서비스로, 이미 디지털화된 텍스트의 주제 분류와는 목적이 다름
- **Kendra (옵션 D)** - 자연어 검색 서비스로 문서 분류가 아닌 검색 기능 제공, 주제별 사전 분류는 수행하지 않음

---