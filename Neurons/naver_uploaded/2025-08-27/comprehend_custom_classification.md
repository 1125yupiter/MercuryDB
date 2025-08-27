---
title: comprehend-custom-classification-training
created: 2025-08-19
modified: 2025-08-19
tags:
- service/comprehend
- method/supervised
- technique/custom-training
- usecase/text-classification
- constraint/multilingual
aliases: ["custom-classification", "comprehend-custom", "text-classifier"]
---

# Amazon Comprehend Custom Classification API를 활용한 맞춤형 텍스트 분류

## 🎯 핵심 포인트

비즈니스 특화된 텍스트 분류가 필요한 경우 Amazon Comprehend Custom Classification API에서, 자체 훈련 데이터로 맞춤형 문서 분류기를 구축할 수 있다.

## 📝 설명

### Amazon Comprehend Custom Classification이 맞춤형 텍스트 분류에 적합한 이유

Amazon Comprehend Custom Classification API는 사용자가 제공하는 레이블된 훈련 데이터를 사용하여 특정 비즈니스 요구사항에 맞는 문서 분류 모델을 구축할 수 있는 관리형 머신러닝 서비스입니다. 미리 정의된 범용 카테고리가 아닌, 기업의 고유한 분류 체계에 따라 텍스트를 분류할 수 있으며, 비즈니스 요구사항 변화에 따라 새로운 카테고리를 추가하거나 기존 모델을 재훈련할 수 있습니다.

### 아키텍처 플로우

```
훈련 데이터 준비 (CSV/JSON/TSV)
↓
S3에 데이터 업로드
↓
Custom Classification 작업 생성
↓
모델 훈련 및 평가
↓
엔드포인트 배포
↓
실시간 분류 추론
```

### Trade-offs 고려사항

**Custom Classification API 장점**:
- 비즈니스 특화 카테고리로 정확한 분류 가능
- 다양한 데이터 형식 지원 (CSV, JSON, TSV)
- 6개 언어 지원 (영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 포르투갈어)
- 완전 관리형 서비스로 인프라 관리 불필요
- 동적 카테고리 추가 및 모델 업데이트 가능

**Custom Classification API 단점**:
- 충분한 양질의 레이블된 훈련 데이터 필요
- 모델 훈련 시간과 비용 발생
- 지원 언어 제한 (아시아 언어 미지원)

**기본 Comprehend API 장점**:
- 즉시 사용 가능한 사전 훈련된 모델
- 훈련 데이터 준비 불필요
- 빠른 구현 속도

**기본 Comprehend API 단점**:
- 범용 카테고리만 지원 (감정, 엔터티 등)
- 비즈니스 특화 분류 불가능
- 커스터마이징 한계

## 🔍 주요개념

### 핵심 개념 비교

- **Custom Classification**: 사용자 정의 카테고리로 텍스트를 분류하는 맞춤형 모델 생성 기능
- **Real-time Classification**: 훈련된 모델을 엔드포인트로 배포하여 실시간 분류 수행
- **Batch Classification**: 대량의 문서를 일괄 처리로 분류하는 비동기 작업
- **Multi-class Classification**: 하나의 문서를 여러 카테고리 중 하나로 분류
- **Multi-label Classification**: 하나의 문서에 여러 레이블을 동시에 할당

### 실전 적용

- **고객 지원 티켓 분류**: 기술지원, 결제문의, 배송문의 등으로 자동 라우팅
- **뉴스 기사 카테고라이제이션**: 정치, 경제, 스포츠, 연예 등 맞춤 카테고리
- **제품 리뷰 분석**: 브랜드별, 제품군별 세분화된 감정 및 주제 분석
- **법률 문서 분류**: 계약서, 소송서류, 특허문서 등 전문 분야 분류
- **의료 텍스트 분류**: 진단서, 처방전, 임상노트 등 의료 전문 분류

## 📝 관련 문제

**Question:** A company needs to automatically categorize customer support tickets into specific business categories like "billing issues," "technical problems," "feature requests," and "account management." They have thousands of historical tickets with proper labels. Which AWS service feature would be most appropriate for this use case?

**Options:**

- A) Amazon Comprehend built-in sentiment analysis
- B) Amazon Textract for document analysis
- C) Amazon Comprehend Custom Classification API
- D) Amazon Translate for multilingual support
- E) Amazon Rekognition for image classification

**정답: C) Amazon Comprehend Custom Classification API**

💡 추가 설명:

- **A) Built-in sentiment analysis** - 감정 분석은 긍정/부정/중립만 분류하므로 비즈니스 특화 카테고리 분류에 부적합
- **B) Textract** - 문서에서 텍스트 추출은 가능하지만 텍스트 분류 기능은 제공하지 않음
- **D) Translate** - 언어 번역 서비스로 텍스트 분류와는 목적이 다름
- **E) Rekognition** - 이미지 분석 서비스로 텍스트 분류에는 적용할 수 없음