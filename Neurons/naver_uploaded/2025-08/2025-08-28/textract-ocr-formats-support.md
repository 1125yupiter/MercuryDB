---

title: textract-ocr-formats-support
created: 2025-08-19
modified: 2025-08-19
tags:
- service/textract
- technology/ocr
- format/pdf
- format/image
- capability/document-analysis
aliases: ["textract", "aws-textract", "ocr"]

---

# Amazon Textract - OCR 및 문서 분석을 위한 지원 파일 형식

## 🎯 핵심 포인트

문서에서 텍스트와 데이터를 추출해야 하는 경우 Amazon Textract에서, PNG, JPEG, TIFF, PDF 형식의 파일을 처리할 수 있다.

## 📝 설명

### Amazon Textract가 문서 처리에 적합한 이유

Amazon Textract는 단순한 OCR을 넘어서는 기계학습 기반 문서 분석 서비스입니다. 인쇄된 텍스트, 손글씨, 양식, 테이블을 자동으로 식별하고 추출할 수 있으며, 문서의 구조적 요소를 이해하여 정확한 데이터 추출이 가능합니다. 동기 및 비동기 처리 모드를 지원하여 단일 페이지 문서부터 대용량 다중 페이지 문서까지 처리할 수 있습니다.

### 아키텍처 플로우

```
문서 업로드 (PNG/JPEG/TIFF/PDF)
    ↓
S3 버킷 또는 바이트 배열
    ↓
Textract API 호출
    ↓
동기 처리 (단일 페이지) / 비동기 처리 (다중 페이지)
    ↓
JSON 형태의 구조화된 텍스트 및 데이터
    ↓
후처리 및 비즈니스 로직 적용
```

### Trade-offs 고려사항

**Amazon Textract 장점**:
- ML 기반으로 높은 정확도의 텍스트 및 데이터 추출
- 양식, 테이블, 핸드라이팅 인식 지원
- 완전 관리형 서비스로 인프라 관리 불필요
- S3와의 원활한 통합

**Amazon Textract 단점**:
- 지원 파일 형식이 제한적 (PNG, JPEG, TIFF, PDF만)
- 수직 텍스트(일본어 세로쓰기 등) 미지원
- 암호로 보호된 PDF 처리 불가
- 사용량 기반 과금으로 대량 처리 시 비용 부담

**기존 OCR 솔루션 장점**:
- 다양한 파일 형식 지원 가능
- 온프레미스 배포로 데이터 보안성 확보
- 커스터마이징 가능성

**기존 OCR 솔루션 단점**:
- 정확도가 상대적으로 낮음
- 복잡한 문서 구조 분석의 한계
- 인프라 관리 및 유지보수 필요

## 🔍 주요개념

### 지원 파일 형식 비교

- **PNG**: 무손실 압축, 투명도 지원, 웹 이미지에 적합
- **JPEG**: 손실 압축, 사진에 최적화, 작은 파일 크기
- **TIFF**: 고품질 이미지, 다중 페이지 지원, 아카이빙에 적합
- **PDF**: 문서 표준 형식, 다중 페이지, 벡터 및 래스터 지원

### 실전 적용

- **금융 서비스**: 대출 신청서, 신용카드 명세서, 보험 청구서에서 핵심 데이터 자동 추출
- **의료 분야**: 환자 정보 양식, 보험 청구서, 처방전에서 중요 정보 디지털화
- **법무 업무**: 계약서, 법정 문서, 신분증에서 구조화된 데이터 추출 및 검증

## 📝 관련 문제

**Question:** A company needs to extract text and structured data from scanned invoices, medical forms, and legal documents in various formats. The solution must handle both single-page and multi-page documents with high accuracy. Which AWS service and supported file formats would be most appropriate for this use case?

**Options:**

- A) Amazon Rekognition with JPEG, PNG, and GIF formats
- B) Amazon Textract with PNG, JPEG, TIFF, and PDF formats  
- C) Amazon Comprehend with TXT, CSV, and JSON formats
- D) AWS Lambda with custom OCR libraries supporting all image formats
- E) Amazon Transcribe with MP3, WAV, and MP4 formats

**정답: B) Amazon Textract with PNG, JPEG, TIFF, and PDF formats**

💡 추가 설명:

- **Amazon Rekognition (A)** - 이미지 분석 및 객체 인식에 특화되어 있으며 GIF는 지원하지 않음. 문서의 구조화된 데이터 추출에는 부적합
- **Amazon Comprehend (C)** - 자연어 처리 서비스로 이미 텍스트화된 데이터 분석에 사용. 스캔된 문서의 OCR 기능 미제공
- **AWS Lambda + Custom OCR (D)** - 구현 복잡성이 높고 관리형 서비스의 이점을 활용할 수 없음. 정확도와 안정성 면에서 불리
- **Amazon Transcribe (E)** - 음성을 텍스트로 변환하는 서비스로 문서 처리와는 무관함