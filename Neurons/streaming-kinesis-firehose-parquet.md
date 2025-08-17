---
title: streaming-kinesis-firehose-parquet
created: 2025-08-17
modified: 2025-08-17
tags:
- service/kinesis-data-firehose
- usecase/streaming-data-ingestion
- format/parquet-conversion
- storage/s3-analytics
- constraint/real-time-processing
aliases: ["kinesis-firehose", "streaming-parquet", "real-time-etl"]
---

# 스트리밍 데이터 수집 및 변환을 위한 Kinesis Data Firehose

## 🎯 핵심 포인트

제조 회사가 스트리밍 데이터를 수집하고 변환하여 S3에 Parquet 형태로 저장한 후 Athena로 분석하려는 경우, Amazon Kinesis Data Firehose에서 완전 관리형 솔루션을 제공할 수 있다.

## 📝 설명

### Kinesis Data Firehose가 스트리밍 데이터 처리에 적합한 이유

Amazon Kinesis Data Firehose는 실시간 스트리밍 데이터를 다양한 목적지로 전송하는 완전 관리형 서비스입니다. 특히 제조업에서 IoT 센서 데이터, 생산 라인 메트릭, 품질 관리 데이터 등을 실시간으로 수집하고 분석 가능한 형태로 변환하는 데 최적화되어 있습니다.

핵심 기능으로는 자동 스케일링, 내장된 데이터 변환 기능, 다양한 포맷 변환 지원(JSON → Parquet), Lambda 함수를 통한 커스텀 변환, 그리고 버퍼링 및 압축 기능이 있습니다. 이를 통해 별도의 인프라 관리 없이도 대용량 스트리밍 데이터를 효율적으로 처리할 수 있습니다.

### 아키텍처 플로우

```
[제조 장비/IoT 센서] 
    ↓ (실시간 데이터 스트림)
[Kinesis Data Firehose]
    ↓ (Lambda 변환 함수 호출)
[데이터 변환 & 포맷 변환]
    ↓ (JSON → Parquet)
[Amazon S3]
    ↓ (쿼리 요청)
[Amazon Athena]
    ↓ (분석 결과)
[대시보드/보고서]
```

### Trade-offs 고려사항

**Kinesis Data Firehose 장점**:
- 완전 관리형 서비스로 운영 부담 최소화
- 자동 스케일링으로 트래픽 변동에 유연 대응
- 내장된 포맷 변환 기능 (JSON, CSV → Parquet, ORC)
- Lambda 함수를 통한 실시간 데이터 변환
- S3, Redshift, OpenSearch 등 다양한 목적지 지원
- 압축 및 암호화 자동 적용

**Kinesis Data Firehose 단점**:
- 최대 1MB 레코드 크기 제한
- 60초 최소 버퍼링 간격으로 준실시간 처리
- 복잡한 데이터 변환 로직의 경우 Lambda 함수 필요
- 순서 보장이 완벽하지 않을 수 있음

**Kinesis Data Streams 장점**:
- 실시간 처리 가능 (밀리초 단위)
- 순서 보장 및 정확히 한 번 처리
- 다중 컨슈머 지원
- 더 세밀한 제어 가능

**Kinesis Data Streams 단점**:
- 직접적인 S3 저장 기능 없음
- 데이터 변환 기능 내장되지 않음
- 별도의 컨슈머 애플리케이션 개발 필요
- 샤드 관리 및 스케일링을 수동으로 처리

## 🔍 주요개념

### 핵심 개념 비교

- **Data Ingestion**: 다양한 소스로부터 데이터를 수집하는 과정으로, Firehose는 HTTP 엔드포인트, Kinesis Data Streams, Lambda 등을 통해 데이터 수집
- **Data Transformation**: Lambda 함수를 활용한 실시간 데이터 변환으로, 필터링, 집계, 포맷 변경 등 수행
- **Format Conversion**: JSON, CSV 등의 소스 데이터를 컬럼형 포맷인 Parquet이나 ORC로 자동 변환하여 쿼리 성능 향상
- **Buffering**: 데이터를 일정 크기나 시간 간격으로 모아서 배치 형태로 전송하여 효율성 증대

### 실전 적용

- **IoT 센서 데이터 수집**: 제조 라인의 온도, 압력, 진동 센서 데이터를 실시간으로 수집하여 예측 유지보수에 활용
- **품질 관리 데이터 분석**: 생산품의 품질 검사 결과를 실시간으로 저장하고 불량률 분석 및 트렌드 모니터링
- **생산 효율성 모니터링**: 장비 가동률, 생산량, 에너지 소비량 등을 실시간으로 추적하여 생산 최적화

## 📝 관련 문제

**Question:** A manufacturing company wants to aggregate streaming data in Amazon S3 and analyze it using Amazon Athena. The company needs a solution that can both ingest and transform streaming data into Apache Parquet format. Which AWS service meets these requirements?

**Options:**

- A) AWS Database Migration Service
- B) AWS Batch  
- C) Amazon Kinesis Data Streams
- D) Amazon Kinesis Data Firehose
- E) Amazon EMR

**정답: D) Amazon Kinesis Data Firehose**

💡 추가 설명:

- **AWS Database Migration Service** - 데이터베이스 마이그레이션 전용 서비스로 스트리밍 데이터 수집에 부적합
- **AWS Batch** - 배치 처리 작업 관리 서비스로 실시간 스트리밍 데이터 처리 불가능
- **Amazon Kinesis Data Streams** - 스트리밍 데이터 수집은 가능하지만 자체적인 변환 및 S3 저장 기능 없음
- **Amazon EMR** - 빅데이터 처리 플랫폼이지만 스트리밍 데이터 수집 및 실시간 변환에는 과도하게 복잡