---

title: textract-structured-data-extraction
created: 2025-08-21
modified: 2025-08-21
tags:
- service/textract
- technique/structured-extraction
- constraint/document-processing
- usecase/form-processing
- comparison/ocr-vs-ml
aliases: ["textract", "structured-ocr", "document-intelligence"]

---

# Amazon Textract를 이용한 구조화된 데이터 추출

## 🎯 핵심 포인트

문서에서 텍스트뿐만 아니라 테이블과 폼의 구조화된 데이터를 추출해야 하는 경우, Amazon Textract를 사용하면 전통적인 OCR과 달리 데이터 관계성과 구조를 보존하면서 자동으로 추출할 수 있다.

## 📝 설명

### Amazon Textract가 문서 처리에 적합한 이유

Amazon Textract는 단순한 광학 문자 인식(OCR)을 넘어서는 머신러닝 기반 문서 분석 서비스입니다. 전통적인 OCR이 문자를 하나씩 인식하여 텍스트 덤프를 생성하는 반면, Textract는 문서의 구조와 의미를 이해하여 다음과 같은 고급 기능을 제공합니다:

**핵심 기능:**
- **텍스트 추출**: 인쇄된 텍스트와 손글씨 모두 인식 (영어 기준)
- **테이블 추출**: 행과 열 구조를 보존하며 각 셀의 위치 정보 제공
- **폼 처리**: 키-값 쌍을 자동으로 식별하고 관계성 유지
- **서명 감지**: 문서 내 서명 위치와 신뢰도 점수 제공
- **쿼리 기능**: 자연어 질문으로 특정 데이터 추출

### 아키텍처 플로우

```
스캔된 문서/이미지 → Amazon Textract → 구조화된 JSON 출력
                                    ↓
                              • 텍스트 블록
                              • 테이블 구조
                              • 키-값 쌍
                              • 바운딩 박스
                              • 신뢰도 점수
                                    ↓
                          데이터베이스/애플리케이션 직접 연동
```

### Trade-offs 고려사항

**Amazon Textract 장점**:
- 구조화된 데이터 자동 추출 (테이블, 폼)
- 높은 정확도 (99%+ 텍스트 인식률)
- 손글씨 인식 지원
- 완전 관리형 서비스 (인프라 관리 불필요)
- 실시간(동기) 및 배치(비동기) 처리 지원
- 다국어 지원 (영어, 독일어, 프랑스어, 스페인어, 이탈리아어, 포르투갈어)

**Amazon Textract 단점**:
- 전통적인 OCR 대비 높은 비용 (텍스트 추출: $1.50/1000페이지, 테이블/폼: $15/1000페이지)
- 손글씨는 영어만 지원
- 동기 처리는 단일 페이지, 5MB 제한
- 일부 복잡한 레이아웃에서 정확도 저하 가능

**전통적인 OCR (Tesseract 등) 장점**:
- 무료 또는 저비용
- 다양한 언어 지원
- 로컬 환경에서 실행 가능
- 커스터마이징 가능

**전통적인 OCR 단점**:
- 구조화된 데이터 추출 불가 (텍스트 덤프만 제공)
- 손글씨 인식 성능 낮음
- 스캔 품질에 민감
- 수동 후처리 작업 필요

## 🔍 주요개념

### 핵심 기능 비교

- **DetectDocumentText**: 기본 OCR 기능, 텍스트와 손글씨만 추출
- **AnalyzeDocument**: 고급 기능, 테이블/폼/레이아웃 분석 포함
- **AnalyzeExpense**: 영수증과 청구서 전용 API
- **AnalyzeID**: 신분증 문서 전용 처리
- **Queries**: 자연어 질문으로 특정 데이터 추출

### 실전 적용

- **금융 서비스**: 대출 신청서에서 신청자 정보, 소득, 대출 조건 자동 추출하여 승인 시간 단축
- **의료 분야**: 환자 접수 양식, 보험 청구서에서 환자 데이터 추출하여 시스템 자동 입력
- **회계 업무**: 수천 건의 청구서와 영수증에서 공급업체, 금액, 날짜 정보를 구조화하여 회계 시스템 연동

## 📝 관련 문제

**Question:** Which of the following is a benefit of using Amazon Textract for document processing in comparison to traditional OCR methods?

**Options:**

- A) Amazon Textract does not support handwriting recognition, while traditional OCR methods can recognize both printed and handwritten text.
- B) Amazon Textract can identify and extract structured data such as tables and forms, while traditional OCR methods only extract text.
- C) Amazon Textract is limited to processing a maximum of 10,000 pages per month, while traditional OCR methods can handle unlimited volumes.
- D) Amazon Textract requires minimal preprocessing of documents, while traditional OCR methods require extensive document preparation.

**정답: B) Amazon Textract can identify and extract structured data such as tables and forms, while traditional OCR methods only extract text.**

💡 추가 설명:

- **A) 틀림** - Amazon Textract는 2021년부터 영어 손글씨 인식을 지원하며, 많은 전통적인 OCR 도구들은 손글씨 인식에 어려움을 겪음
- **C) 틀림** - Amazon Textract에는 월 페이지 처리 제한이 없으며, 비동기 처리로 문서당 최대 1,000페이지까지 처리 가능
- **D) 부분적으로 맞지만 주요 장점 아님** - 전처리 최소화는 장점이지만 구조화된 데이터 추출이 핵심 차별점