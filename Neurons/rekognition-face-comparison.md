---
title: aws-rekognition-face-detection-comparison-celebrities
created: 2025-08-15
modified: 2025-08-15
tags:
- service/amazon-rekognition
- feature/face-detection
- usecase/face-comparison
- data-source/s3-integration
- ai-service/computer-vision
aliases: ["Rekognition", "Face Detection", "Celebrity Recognition"]
---

# Amazon Rekognition - 얼굴 검출 및 비교 시스템

## 🎯 핵심 포인트
사용자 정의 얼굴 데이터베이스와 비교가 필요한 경우 SearchFaces 기능에서, 미리 정의된 유명인사 인식이 필요한 경우 RecognizeCelebrities 기능을 사용할 수 있다.

## 📝 설명

### Amazon Rekognition이 얼굴 인식 프로젝트에 적합한 이유
Amazon Rekognition은 완전 관리형 컴퓨터 비전 서비스로, 다양한 얼굴 인식 및 분석 기능을 제공합니다. 사용자는 별도의 ML 모델 학습 없이도 API 호출만으로 얼굴 검출, 비교, 인식 작업을 수행할 수 있습니다. S3 통합을 통한 대용량 이미지 처리와 실시간 바이너리 데이터 전송을 모두 지원하여 유연한 아키텍처 구성이 가능합니다.

### 아키텍처 플로우
```
이미지 입력 (S3/Direct Upload)
    ↓
Amazon Rekognition API 호출
    ↓
기능별 처리 분기:
├─ DetectFaces → 얼굴 속성 분석 (나이, 감정, 성별)
├─ SearchFaces → Face Collection과 매칭 검색
├─ RecognizeCelebrities → 유명인사 DB 매칭
├─ CompareFaces → 두 얼굴 직접 비교
└─ DetectLabels → 객체/장면 인식
    ↓
JSON 형태 결과 반환 (신뢰도 점수 포함)
```

### Trade-offs 고려사항

**SearchFaces 장점**:
- 사용자 정의 얼굴 컬렉션 생성 가능
- 특정 조직/그룹의 얼굴 데이터베이스 구축
- 높은 정확도의 1:N 얼굴 매칭

**SearchFaces 단점**:
- Face Collection 사전 구축 필요
- IndexFaces 과정에서 추가 비용 발생 (이미지당 $0.001)
- TPS 제한으로 대량 인덱싱 시 시간 소요 (기본 5 TPS)
- 컬렉션 관리 오버헤드

**RecognizeCelebrities 장점**:
- 즉시 사용 가능한 유명인사 DB
- 별도 학습/인덱싱 불필요
- 정치인, 배우, 스포츠 선수 등 광범위한 커버리지

**RecognizeCelebrities 단점**:
- 아마존이 미리 정의한 인물만 인식 가능
- 사용자 정의 인물 추가 불가
- 지역별/문화별 유명인사 편향 가능성

## 🔍 주요개념

### 핵심 기능 비교
- **DetectFaces**: 얼굴 검출 및 속성 분석 (나이, 감정, 성별 등)
- **SearchFaces**: 사용자 정의 Face Collection에서 매칭 얼굴 검색
- **RecognizeCelebrities**: 아마존 유명인사 DB와 매칭하여 셀럽 인식
- **CompareFaces**: 두 이미지의 얼굴을 직접 비교하여 동일인 여부 판단
- **IndexFaces**: Face Collection에 얼굴 추가 (이미지당 $0.001, 기본 5 TPS 제한)
- **DetectLabels**: 이미지 내 객체, 장면, 활동, 개념 등 광범위한 라벨 검출

### 실전 적용
- 보안 시스템에서 직원 출입 관리 (SearchFaces로 직원 DB 구축, 100명 인덱싱 시 $0.10 비용에 20초 소요)
- 미디어 플랫폼에서 유명인사 자동 태깅 (RecognizeCelebrities 활용, 별도 인덱싱 불필요)
- 소셜 미디어에서 사진 자동 분류 및 검색 (DetectLabels로 컨텐츠 태깅, TPS 기준 처리 속도 결정)

## 📝 관련 문제

**Question:** You are developing a face recognition system that needs to identify employees from uploaded photos against a company database of known employee faces. Which Amazon Rekognition feature should you use?

**Options:**
- A) DetectFaces
- B) RecognizeCelebrities  
- C) SearchFaces
- D) DetectLabels
- E) DetectText

**정답: C) SearchFaces**

💡 추가 설명:
- **DetectFaces** - 얼굴을 검출하고 속성을 분석하지만 기존 DB와 비교하지 않음
- **RecognizeCelebrities** - 아마존의 유명인사 DB만 사용하며 사용자 정의 직원 DB는 지원하지 않음
- **DetectLabels** - 객체나 장면을 인식하는 기능으로 얼굴 인식과는 무관
- **DetectText** - 이미지에서 텍스트를 추출하는 OCR 기능으로 얼굴 인식과 무관
- **IndexFaces 비용**: 직원 1000명 인덱싱 시 $1.00 비용, 기본 5 TPS로 200초(3분 20초) 소요