---
title: realtime-kinesis-lambda-sql.md
created: 2025-08-17
modified: 2025-08-17
tags:
- service/kinesis
- service/lambda
- service/athena
- constraint/real-time
- constraint/low-latency
aliases: ["firehose-realtime-sql", "kinesis-sql-streaming", "gzip-to-sql"]
---

# 🔄 Kinesis Data Analytics + Lambda for Real-Time SQL on Firehose Data

## 🎯 핵심 포인트

온프레미스에서 GZIP으로 압축된 데이터를 Firehose로 인제스트한 경우, **Kinesis Data Analytics에서 Lambda로 변환 후 SQL 쿼리**를 실행하면 최소 지연으로 실시간 인사이트를 얻을 수 있다.

## 📝 설명

### Kinesis Data Analytics가 실시간 SQL 분석에 적합한 이유

- **데이터 변환**: Firehose에서 전달된 GZIP 압축 데이터를 Kinesis Analytics 내부 Lambda Processor로 변환 가능  
- **SQL 처리**: 변환된 JSON/CSV 형태의 데이터를 바로 SQL로 쿼리 가능  
- **실시간성 보장**: 데이터가 도착하자마자 처리되어 최소 지연(latency milliseconds 단위)으로 결과 제공  
- **운영 효율성**: 별도의 배치 저장이나 복잡한 파이프라인 없이 간단한 구성이 가능  

Athena나 Glue는 배치/준실시간이므로 지연이 크고, KCL은 Firehose와 직접 호환되지 않아 부적합하다.

### 아키텍처 플로우

[On-Prem App] --(GZIP Data)--> [Kinesis Firehose]
↓
[Kinesis Data Analytics] --(Lambda Processor: decompress/transform)-->
↓
[SQL Queries in Kinesis Analytics]
↓
[Real-time Insights / Dashboards / Downstream Systems]


### Trade-offs 고려사항

**Kinesis Data Analytics 장점**:
- SQL 기반 실시간 스트리밍 분석
- Lambda Processor로 GZIP, JSON 변환 지원
- 최소 지연으로 대시보드/모니터링 가능

**Kinesis Data Analytics 단점**:
- SQL 기능은 제한적 (복잡한 분석에는 부족)
- 추가 요금 발생 (Firehose + Analytics + Lambda 조합)

**대안 (Athena) 장점**:
- S3 데이터에 표준 SQL 쿼리 가능
- 서버리스로 비용 효율적

**대안 (Athena) 단점**:
- 실시간 처리 불가 (분 단위 이상 지연)
- Firehose 스트림을 직접 ingest하지 못함

**대안 (Glue Streaming) 장점**:
- 복잡한 ETL 처리 가능
- Spark 기반 유연한 변환

**대안 (Glue Streaming) 단점**:
- Firehose 직접 소스 불가
- 관리 및 성능 튜닝 부담

## 🔍 주요개념

### Firehose vs Kinesis Data Streams
- **Firehose**: 자동 적재 서비스 (S3, Redshift, OpenSearch로 전달). 실시간 처리보다는 안정적 수집/저장 목적.
- **Data Streams**: 초저지연 스트리밍 데이터 소비. KCL, Analytics 애플리케이션에서 직접 읽기 가능.

### Lambda Processor in Kinesis Analytics
- GZIP 해제, JSON 변환 등 데이터 전처리를 Lambda 함수로 수행
- 변환된 레코드가 SQL 엔진으로 전달됨

### 실전 적용
- **보안 로그 모니터링**: 압축된 로그를 해제하고 실시간 이상 탐지  
- **IoT 센서 데이터 분석**: GZIP으로 수집된 센서 스트림을 변환 후 SQL 집계  
- **금융 거래 모니터링**: 거래 스트림을 압축 해제 → SQL로 Fraud 탐지 Rule 실행  

## 📝 관련 문제

**Question:**  
A Data Scientist is using an Amazon Data Firehose delivery stream to ingest GZIP-compressed data from on-premises. They want to run SQL queries for real-time insights with the least latency. Which option is correct?

**Options:**
- A) Transform the data with Amazon KCL and deliver to Amazon OpenSearch  
- B) Store in Amazon S3 and use Athena for queries  
- C) Use AWS Glue streaming ETL on the Firehose data  
- D) Use a Kinesis Data Analytics application with AWS Lambda to transform data  

**정답: D) Kinesis Data Analytics + Lambda**

💡 추가 설명:

- **A (KCL + OpenSearch)** - KCL은 Data Streams 전용이며 Firehose에는 적용 불가  
- **B (S3 + Athena)** - Athena는 배치성 분석 도구, 실시간 불가  
- **C (Glue Streaming)** - Firehose를 직접 소스로 사용 불가, Data Streams 필요  
- **D (Kinesis Analytics + Lambda)** - GZIP 해제 및 SQL 기반 실시간 분석 가능 → ✅ 정답  
