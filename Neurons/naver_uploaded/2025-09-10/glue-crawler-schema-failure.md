---
title: glue-crawler-schema-failure
created: 2025-08-28
modified: 2025-08-28
tags:
- service/glue
- problem/schema-detection
- constraint/data-format
- troubleshooting/classifier
- usecase/data-catalog
aliases: ["glue-crawler-fail", "schema-creation-error", "classifier-zero"]
---

# AWS Glue 크롤러 스키마 생성 실패 - 분류기 인식 문제

## 🎯 핵심 포인트

외부 데이터 제공업체의 비표준 형식 데이터를 S3에서 크롤링할 때 모든 분류기가 0.0 확실성을 반환하면, Glue 크롤러는 데이터 형식을 인식하지 못해 스키마 생성에 실패할 수 있다.

## 📝 설명

### AWS Glue 크롤러가 스키마 생성에 실패하는 이유

AWS Glue 크롤러는 S3의 데이터 파일들을 분석하여 자동으로 테이블 스키마를 생성하는 서비스입니다. 크롤러는 내장된 여러 분류기(Classifier)를 사용하여 파일 형식을 인식하고, 각 분류기는 해당 데이터가 자신이 처리할 수 있는 형식인지에 대한 확실성(confidence) 점수를 0.0~1.0 범위로 반환합니다.

모든 분류기가 0.0 확실성을 반환한다는 것은 어떤 분류기도 데이터 형식을 인식하지 못했다는 의미이며, 이 경우 크롤러는 스키마를 생성할 수 없습니다.

### 아키텍처 플로우

```
S3 Data Files
      ↓
Glue Crawler 시작
      ↓
내장 분류기들이 파일 분석
      ↓
├─ CSV Classifier → 확실성 0.0
├─ JSON Classifier → 확실성 0.0  
├─ Parquet Classifier → 확실성 0.0
├─ XML Classifier → 확실성 0.0
└─ Custom Classifier → 확실성 0.0
      ↓
모든 분류기 실패 (확실성 0.0)
      ↓
스키마 생성 실패 ❌
```

### Trade-offs 고려사항

**내장 분류기 장점**:
- 표준 형식(CSV, JSON, Parquet 등)에 대한 자동 인식
- 추가 설정 없이 즉시 사용 가능
- AWS에서 최적화된 성능

**내장 분류기 단점**:
- 비표준 형식 데이터 인식 불가
- 외부 업체의 독특한 형식 처리 한계
- 복합 형식이나 특수 구분자 인식 어려움

**커스텀 분류기 장점**:
- 특정 비즈니스 데이터 형식에 맞춘 정확한 인식
- 정규식이나 Grok 패턴으로 유연한 형식 정의
- 외부 데이터 제공업체 형식에 특화 가능

**커스텀 분류기 단점**:
- 추가 개발 및 유지보수 필요
- 복잡한 패턴 작성 시 성능 저하 가능
- 데이터 형식 변경 시 분류기 업데이트 필요

## 🔍 주요개념

### 분류기 종류 비교

- **내장 분류기**: AWS에서 제공하는 표준 형식 인식기 (CSV, JSON, Parquet, Avro, ORC, XML 등)
- **커스텀 분류기**: 사용자가 정의한 형식을 인식하기 위한 정규식 또는 Grok 패턴 기반 분류기

### 실전 적용

- 외부 마케팅 데이터 업체가 파이프(|) 구분자를 사용하는 특수 CSV 형식 제공
- 금융 데이터 제공업체가 고정 폭 텍스트 형식으로 데이터 전송
- IoT 센서 데이터가 JSON이지만 매우 중첩된 비정형 구조로 구성된 경우

## 📝 관련 문제

**Question:** You work for a web retailer where you need to analyze data produced for your company by an outside market data provider. You need to produce recommendations based on patterns in user preferences by demographic found in the supplied data. You have stored the data in one of your company's S3 buckets. You have created a Glue crawler that you have configured to crawl the data on S3 and you have written a custom classifier. Unfortunately, the crawler failed to create a schema. Why might the Glue crawler have failed in this way?

**Options:**

- A) You did not add an exclude pattern when you configured the data store.
- B) The IAM role you assigned to the crawler has the AWSGlueServiceRole managed policy attached plus an inline policy that allows read access to your S3 bucket.
- C) All the classifiers returned a certainty of 0.0
- D) You chose to create a single schema for each S3 path.

**정답: C) All the classifiers returned a certainty of 0.0**

💡 추가 설명:

- **A) 제외 패턴 미설정** - 제외 패턴은 크롤링하지 않을 파일을 지정하는 선택사항으로, 없어도 스키마 생성에는 영향 없음
- **B) IAM 역할 설정** - 이는 올바른 권한 설정으로 문제가 아님. AWSGlueServiceRole과 S3 읽기 권한은 적절한 구성
- **D) 단일 스키마 선택** - 각 S3 경로별 단일 스키마 생성은 정상적인 설정 옵션 중 하나
- **핵심 원인** - 외부 데이터 제공업체의 특수 형식을 어떤 분류기도 인식하지 못해 모두 0.0 확실성을 반환한 것이 스키마 생성 실패의 직접적 원인

---