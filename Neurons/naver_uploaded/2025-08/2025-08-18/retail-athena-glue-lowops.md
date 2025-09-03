---
title: retail-athena-glue-lowops
created: 2025-08-16
modified: 2025-08-16
tags:
- problem/data-analysis
- service/athena
- service/glue
- constraint/lowops
- industry/retail
aliases: ["retail-data-analysis", "athena-glue-s3", "lowops-sql"]
---

# 리테일 데이터 분석을 위한 Amazon Athena와 AWS Glue 활용

## 🎯 핵심 포인트

소매 회사가 S3에 저장된 구조화 및 반구조화 데이터를 분석할 경우, AWS Glue와 Amazon Athena를 사용하면 최소한의 운영 부담으로 SQL 쿼리를 실행할 수 있다.

## 📝 설명

### Amazon Athena와 AWS Glue가 리테일 데이터 분석에 적합한 이유

소매 회사에서 발생하는 대규모 구조화 데이터(예: CSV, Parquet 형식의 판매 데이터)와 반구조화 데이터(예: JSON 형식의 고객 행동 로그)를 S3 버킷에 저장하고, 이를 SQL 쿼리로 분석해야 하는 상황에서 AWS Glue와 Amazon Athena는 서버리스 아키텍처로 운영 부담을 최소화하며 효율적인 데이터 분석을 가능하게 한다.

- **AWS Glue**:
  - **핵심 특징**: 서버리스 데이터 카탈로그 및 ETL 서비스로, S3에 저장된 데이터를 크롤링하여 스키마를 자동으로 추론하고 Glue Data Catalog에 메타데이터를 저장한다. 이를 통해 CSV, JSON, Parquet 등 다양한 형식의 데이터를 구조화된 형태로 관리할 수 있다.
  - **작동 방식**: Glue 크롤러는 S3 버킷의 데이터를 스캔하여 테이블 정의를 생성하고, 필요 시 ETL 작업을 통해 데이터를 변환(예: 형식 변환, 데이터 정제)한다. 이 과정은 서버리스 방식으로 실행되며, 사용자는 클러스터 관리 없이 작업을 스케줄링하거나 즉시 실행할 수 있다.
  - **고려사항**: Glue는 S3 데이터의 메타데이터 관리와 ETL 작업에 최적화되어 있으며, 대규모 데이터셋에서도 빠르게 동작한다. 하지만 복잡한 변환 로직이 필요할 경우 스크립트 작성(Python/Spark)이 필요할 수 있다.

- **Amazon Athena**:
  - **핵심 특징**: 서버리스 쿼리 서비스로, S3에 저장된 데이터에 대해 표준 SQL 쿼리를 직접 실행할 수 있다. Glue Data Catalog와 통합되어 별도의 데이터 로드 없이 즉시 쿼리를 실행할 수 있다.
  - **작동 방식**: Athena는 Presto 기반 쿼리 엔진을 사용하며, S3 데이터에 대한 쿼리 결과를 빠르게 반환한다. 사용자는 쿼리한 데이터 스캔량에 따라 요금을 지불하며, 서버 관리나 용량 계획이 필요 없다.
  - **고려사항**: 쿼리 성능은 데이터 파티셔닝과 압축 형식(예: Parquet, ORC)에 크게 의존한다. 최적화되지 않은 데이터 구조는 비용 증가로 이어질 수 있으므로, Glue로 데이터를 파티셔닝하거나 최적화하는 것이 권장된다.

- **실제 적용 시 고려사항**:
  - S3 데이터의 구조화 정도에 따라 Glue 크롤러의 설정(예: 파일 경로, 파일 형식 지정)이 중요하다.
  - Athena 쿼리 비용 최적화를 위해 S3에 저장된 데이터를 Parquet 또는 ORC 같은 컬럼형 형식으로 변환하고, 적절한 파티셔닝(예: 날짜별, 지역별)을 적용해야 한다.
  - 소매 환경에서는 판매 데이터, 고객 로그, 재고 데이터 등 다양한 데이터 소스를 통합 분석해야 하므로, Glue를 활용해 데이터를 정규화하거나 조인 가능한 형태로 전처리하는 것이 유용하다.

### 아키텍처 플로우

```
S3 버킷 (구조화/반구조화 데이터)
        ↓
AWS Glue 크롤러 (데이터 크롤링 및 메타데이터 생성)
        ↓
Glue Data Catalog (스키마 및 테이블 정의 저장)
        ↓
Amazon Athena (SQL 쿼리로 데이터 분석)
        ↓
쿼리 결과 (CSV, JSON 출력 또는 BI 도구로 전송)
```

### Trade-offs 고려사항

**AWS Glue와 Amazon Athena 장점**:
- 서버리스 아키텍처로 클러스터 관리나 용량 계획이 필요 없음.
- S3 기반의 확장 가능한 저장소와 통합되어 대규모 데이터 처리에 적합.
- Athena는 사용한 쿼리 스캔량에 따라 요금 부과, Glue는 크롤링 및 ETL 작업 시간에 따라 비용 발생.
- 빠른 설정과 즉각적인 쿼리 실행 가능, 초기 설정 후 운영 부담 최소.

**AWS Glue와 Amazon Athena 단점**:
- Athena 쿼리 성능은 데이터 파티셔닝과 압축 형식에 의존하며, 최적화되지 않은 데이터는 비용 증가 가능.
- Glue 크롤러는 복잡한 데이터 구조에서 스키마 추론이 부정확할 수 있어 수동 조정이 필요할 수 있음.
- 실시간 데이터 분석에는 부적합하며, 배치 처리 기반 분석에 최적.

**Amazon Redshift 장점**:
- 대규모 데이터 웨어하우징에 적합하며, 복잡한 조인 및 집계 쿼리에 최적화.
- BI 도구(예: QuickSight)와의 통합이 뛰어남.

**Amazon Redshift 단점**:
- 클러스터 프로비저닝과 관리 필요, 운영 부담이 큼.
- S3에서 데이터를 Redshift로 로드하는 추가 ETL 프로세스 필요.

**Kinesis Data Analytics 장점**:
- 스트리밍 데이터 처리에 특화되어 실시간 분석 가능.
- 서버리스로 동작하여 관리 부담이 적음.

**Kinesis Data Analytics 단점**:
- S3의 정적 데이터에 대한 직접 쿼리는 불가능하며, 스트리밍 데이터 소스(Kinesis Streams, Firehose)에 의존.
- 소매 환경의 배치 데이터 분석에는 적합하지 않음.

**Amazon EMR 장점**:
- Hive, Spark SQL 등 다양한 쿼리 엔진 지원으로 복잡한 데이터 처리 가능.
- 대규모 병렬 처리에 적합.

**Amazon EMR 단점**:
- 클러스터 프로비저닝 및 관리 필요, 운영 부담이 큼.
- 서버리스 솔루션에 비해 설정 및 유지보수 복잡성 증가.

## 🔍 주요개념

### 핵심 개념 비교

- **AWS Glue Data Catalog**: S3 데이터의 메타데이터(스키마, 테이블 정의)를 저장하는 중앙화된 저장소. Athena, Redshift, EMR 등 다양한 서비스와 통합되어 데이터 검색 및 관리 효율성을 높임.
- **Amazon Athena**: S3 데이터를 직접 쿼리하는 서버리스 SQL 엔진. Presto 기반으로 동작하며, Glue Data Catalog의 메타데이터를 활용해 빠른 쿼리 실행 가능.

### 실전 적용

- **판매 데이터 분석**: 매장별 일일 판매 데이터를 Parquet 형식으로 S3에 저장하고, Glue로 파티셔닝(예: 연도/월/일) 후 Athena로 매출 트렌드 분석.
- **고객 행동 분석**: 웹사이트 클릭 로그(JSON)를 S3에 저장하고, Glue로 정규화 후 Athena로 고객 세그먼트별 행동 패턴 분석.
- **재고 최적화**: 재고 데이터(CSV)를 S3에 저장하고, Glue로 데이터 정제 후 Athena로 재고 부족 위험 예측 쿼리 실행.

## 📝 관련 문제

**Question:** A retail company stores structured and semi-structured data in an S3 bucket and requires a Machine Learning Specialist to analyze the data using SQL queries with the least amount of operational overhead. Which approach satisfies this requirement?

**Options:**
- A) Create a pipeline for transforming data using AWS Data Pipeline and use Amazon Redshift to run SQL queries.
- B) Catalog the data using AWS Glue and use Amazon Athena to run SQL queries.
- C) Invoke a Lambda function to transform the data and use a Kinesis Data Analytics application to run SQL queries.
- D) Ingest the data through a Kinesis Firehose delivery stream and use Lambda transformation to convert the data. Use an Amazon EMR cluster to run SQL queries.
- E) Use Amazon S3 Select to directly query the data without any preprocessing.

**정답: B) Catalog the data using AWS Glue and use Amazon Athena to run SQL queries.**

💡 추가 설명:
- **오답 옵션 A**: AWS Data Pipeline은 ETL 작업을 오케스트레이션하지만, Redshift는 클러스터 관리와 데이터 로드 작업이 필요해 운영 부담이 큼.
- **오답 옵션 C**: Kinesis Data Analytics는 스트리밍 데이터 처리에 특화되어 있으며, S3의 정적 데이터에 대한 직접 SQL 쿼리를 지원하지 않음.
- **오답 옵션 D**: EMR은 강력한 처리 능력을 제공하지만, 클러스터 관리와 설정이 필요해 운영 부담이 큼.
- **오답 옵션 E**: S3 Select는 간단한 쿼리에 적합하지만, 복잡한 SQL 쿼리나 대규모 데이터 분석에는 제한적이며 Glue/Athena 조합에 비해 유연성이 떨어짐.

---