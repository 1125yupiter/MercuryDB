---

title: athena-parquet-performance
created: 2025-08-16
modified: 2025-08-16
tags:
- service/athena
- format/parquet
- performance/query-optimization
- storage/s3
- constraint/large-volume
aliases: ["athena-performance", "parquet-optimization", "s3-query-tuning"]

---

# Amazon Athena 대용량 데이터 쿼리 성능 최적화

## 🎯 핵심 포인트

S3에 매분 수천 개의 레코드가 저장되는 대용량 데이터 환경에서 Athena 쿼리 성능을 개선해야 하는 경우, Apache Parquet 포맷을 사용하여 쿼리 속도와 비용 효율성을 동시에 향상시킬 수 있다.

## 📝 설명

### Apache Parquet이 대용량 Athena 쿼리에 적합한 이유

Apache Parquet은 컬럼형 저장 구조를 기반으로 하는 오픈소스 포맷으로, 분석 워크로드에 최적화되어 있습니다. 기존 텍스트 포맷 대비 2배 빠른 언로드 성능과 6배 적은 저장 공간을 제공하며, Athena의 스캔 기반 과금 모델에서 비용 절감 효과가 큽니다.

Parquet의 핵심 장점은 Predicate Pushdown 지원입니다. 각 데이터 블록에 저장된 통계 정보(min/max 값)를 활용해 쿼리 조건에 맞지 않는 블록을 사전에 제외하여 실제 스캔하는 데이터량을 대폭 줄입니다.

### 아키텍처 플로우

```
웹서비스 데이터 생성 (매분 수천 레코드)
         ↓
    S3 버킷 저장 (CSV/JSON)
         ↓
  AWS Glue ETL 작업 실행
         ↓
   Parquet 포맷 변환
         ↓
파티셔닝된 S3 저장 (날짜/시간별)
         ↓
   Athena 쿼리 실행
         ↓
향상된 성능 및 비용 절감
```

### Trade-offs 고려사항

**Apache Parquet 장점**:
- 컬럼형 구조로 필요한 컬럼만 스캔 (I/O 최적화)
- 우수한 압축률로 저장 비용 및 스캔 비용 절감
- Predicate Pushdown으로 불필요한 데이터 블록 건너뛰기
- Snappy, GZIP 등 압축 알고리즘 지원

**Apache Parquet 단점**:
- 초기 변환 작업이 필요 (ETL 파이프라인 구축)
- 실시간 데이터 추가 시 복잡성 증가
- 작은 파일 다수 생성 시 Small Files Problem 발생 가능

**CSV/TSV 장점**:
- 단순한 구조로 데이터 확인 용이
- 별도 변환 없이 바로 사용 가능

**CSV/TSV 단점**:
- 행 기반 포맷으로 전체 행 스캔 필요
- 압축 효율성 낮음
- 스키마 정보 부재로 데이터 타입 추론 필요

**JSON 단점**:
- 스키마가 없어 파싱 오버헤드 존재
- 중복된 키 정보로 저장 공간 비효율
- 압축해도 쿼리 성능 개선 제한적

## 🔍 주요개념

### 저장 포맷 비교

- **행 기반 포맷 (CSV, TSV, JSON)**: 레코드 단위로 데이터 저장, 전체 행 스캔 필요
- **컬럼형 포맷 (Parquet, ORC)**: 컬럼별로 데이터 저장, 필요한 컬럼만 선택적 스캔

### 실전 적용

- **실시간 로그 분석**: 매분 생성되는 웹 액세스 로그를 Parquet으로 변환하여 시간대별 트래픽 분석
- **IoT 센서 데이터 처리**: 다양한 센서에서 수집되는 시계열 데이터를 컬럼형으로 저장하여 특정 센서 값만 조회
- **비즈니스 인텔리전스**: 매출 데이터를 Parquet으로 저장하여 월별, 지역별 집계 쿼리 성능 최적화

## 📝 관련 문제

**Question:** A web service is producing thousands of records into an Amazon S3 bucket every minute. A Data Engineering team performs queries against these data using Amazon Athena. Due to the large volume of data, most of the SQL queries run slowly. The team seeks a solution that will improve query performance. How should the solution be implemented?

**Options:**

- A) Store the data in CSV format
- B) Store the data in TSV format  
- C) Store the data in Apache Parquet format
- D) Store the data in minified JSON format
- E) Increase Athena query concurrency limits

**정답: C) Store the data in Apache Parquet format**

💡 추가 설명:

- **CSV/TSV 포맷** - 행 기반 구조로 쿼리 시 전체 행을 스캔해야 하므로 대용량 데이터에서 성능 저하 발생
- **Minified JSON** - 파일 크기는 줄어들지만 스키마 부재와 파싱 오버헤드로 쿼리 성능 개선 제한적
- **동시성 증가** - 근본적인 스캔 성능 문제를 해결하지 못하며 비용만 증가