---

title: etl-migration-glue-pyspark
created: 2025-08-22
modified: 2025-08-22
tags:
- service/glue
- migration/on-premises
- constraint/serverless
- technique/pyspark
- usecase/etl-batch
aliases: ["glue-etl", "pyspark-migration", "etl-cloud"]

---

# ETL 마이그레이션에서 AWS Glue와 PySpark 활용

## 🎯 핵심 포인트

온프레미스 PySpark ETL 프로세스를 클라우드로 마이그레이션할 경우 AWS Glue에서, 기존 PySpark 로직을 재사용하면서 서버 관리 없이 스케줄 기반 배치 처리를 할 수 있다.

## 📝 설명

### AWS Glue가 ETL 마이그레이션에 적합한 이유

AWS Glue는 완전 관리형 ETL 서비스로, Apache Spark 기반의 서버리스 환경을 제공합니다. PySpark 코드를 완전히 지원하므로 기존 온프레미스 ETL 로직을 최소한의 변경으로 마이그레이션할 수 있습니다. Glue Trigger를 통한 스케줄링 기능과 S3와의 네이티브 통합으로 대용량 데이터 처리에 최적화되어 있습니다.

### 아키텍처 플로우

```
Raw Data → S3 Input Bucket
    ↓
AWS Glue Trigger (Schedule)
    ↓
AWS Glue ETL Job (PySpark)
    ↓ (기존 로직 재사용)
Data Transformation
    ↓
S3 Processed Bucket → Downstream Systems
```

### Trade-offs 고려사항

**AWS Glue 장점**:
- PySpark 네이티브 지원으로 기존 코드 재사용 가능
- 완전 관리형 서비스로 서버 관리 불필요
- Glue Trigger로 스케줄링 내장 지원
- S3와 완벽한 통합으로 대용량 데이터 처리 최적화
- CloudWatch를 통한 모니터링 및 로깅

**AWS Glue 단점**:
- 콜드 스타트로 인한 초기 지연 시간
- 실행 중 스케일링 제한
- 커스텀 라이브러리 사용 시 추가 설정 필요

**AWS Lambda 단점**:
- 15분 실행 시간 제한으로 대용량 데이터 처리 불가
- 메모리 제한 (최대 10GB)
- PySpark 네이티브 지원 없음

**Kinesis Data Analytics 단점**:
- 실시간 스트리밍 서비스로 배치 스케줄링 부적합
- SQL 기반으로 PySpark 코드 재사용 불가
- 정기 스케줄 실행 개념과 맞지 않음

## 🔍 주요개념

### 배치 vs 스트리밍 처리 비교

- **배치 처리 (AWS Glue)**: 정해진 시간에 대량의 데이터를 일괄 처리하는 방식. 스케줄 기반 ETL에 적합
- **스트리밍 처리 (Kinesis)**: 실시간으로 연속적인 데이터를 처리하는 방식. 실시간 분석에 적합

### 실전 적용

- 일일/주간 매출 데이터 통합 및 리포팅용 데이터 생성
- 로그 데이터 정제 및 데이터 웨어하우스로 적재
- 다중 소스 고객 데이터 통합 및 마스터 데이터 생성

## 📝 관련 문제

**Question:** A Data Scientist is tasked with migrating an on-premises ETL process to the cloud. The current process runs on a schedule and uses PySpark to consolidate and format multiple large data sources into a single consolidated output. The requirements are: combine multiple data sources, reuse existing PySpark logic, run on existing schedule, and minimize managed servers. Which architecture should be used?

**Options:**

- A) Use a persistent EMR cluster with Lambda scheduling
- B) Use AWS Glue ETL job with PySpark and Glue triggers for scheduling
- C) Use Lambda function with Python to implement PySpark logic
- D) Use Kinesis Data Analytics for real-time SQL transformations
- E) Use Step Functions to orchestrate multiple Lambda functions

**정답: B) AWS Glue ETL job with PySpark and Glue triggers**

💡 추가 설명:

- **Option A** - 지속적인 EMR 클러스터는 서버 관리가 필요하여 요구사항 위배
- **Option C** - Lambda는 15분 실행 제한과 PySpark 미지원으로 대용량 ETL 부적합
- **Option D** - 실시간 스트리밍 서비스로 배치 스케줄링과 PySpark 재사용 불가
- **Option E** - 복잡한 오케스트레이션으로 단순한 ETL 요구사항에 과도한 설계