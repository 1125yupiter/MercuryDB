---

title: datalake-glue-serverless-etl
created: 2025-08-17
modified: 2025-08-17
tags:
- service/glue
- architecture/serverless
- usecase/datalake
- pipeline/event-driven
- storage/s3
aliases: ["aws-glue-datalake", "serverless-etl", "glue-crawler"]

---

# AWS 서버리스 데이터레이크와 이벤트 기반 ETL 파이프라인

## 🎯 핵심 포인트

S3에 다양한 타입의 데이터를 저장하는 데이터레이크에서 서버리스 메타데이터 저장과 이벤트 기반 ETL이 필요한 경우, AWS Glue Data Catalog와 CloudWatch Events 기반 Lambda 트리거를 통해 완전 관리형 파이프라인을 구축할 수 있다.

## 📝 설명

### AWS Glue가 서버리스 데이터레이크에 적합한 이유

AWS Glue는 완전 관리형 ETL 서비스로서 서버 관리 없이 데이터 통합 작업을 수행할 수 있습니다. Glue Data Catalog는 Apache Hive 메타스토어와 호환되면서도 서버리스 아키텍처를 제공하여 운영 오버헤드를 제거합니다. Crawler는 S3의 다양한 데이터 포맷(JSON, Parquet, CSV, Avro 등)을 자동으로 스캔하여 스키마를 추출하고 메타데이터 테이블을 생성합니다.

### 아키텍처 플로우

```
S3 Data Lake (Multiple Data Types)
    ↓
AWS Glue Crawler (Schema Discovery)
    ↓
Glue Data Catalog (Serverless Metadata Store)
    ↓
CloudWatch Events (Crawler Completion)
    ↓
Lambda Function (Event Handler)
    ↓
AWS Glue ETL Job (Data Processing)
    ↓
Processed Data → S3/Data Warehouse
```

### Trade-offs 고려사항

**AWS Glue Data Catalog 장점**:
- 완전 서버리스로 인프라 관리 불필요
- 다양한 AWS 서비스와 네이티브 통합 (Athena, EMR, Redshift)
- 자동 스키마 진화 감지
- Apache Hive 메타스토어 호환성

**AWS Glue Data Catalog 단점**:
- 대용량 테이블에서 Crawler 실행 시간이 길어질 수 있음
- 복잡한 중첩 데이터 구조 처리에 제한

**Apache Hive Metastore on EMR 장점**:
- 기존 Hadoop 생태계와의 완벽한 호환성
- 복잡한 메타데이터 작업에 대한 세밀한 제어

**Apache Hive Metastore on EMR 단점**:
- EMR 클러스터 관리 및 운영 필요 (서버리스 위배)
- 고가용성 구성의 복잡성
- 지속적인 인프라 비용 발생

## 🔍 주요개념

### 이벤트 기반 vs 스케줄 기반 트리거

- **CloudWatch Events**: 특정 AWS 서비스 상태 변경을 실시간으로 감지하여 후속 작업 실행
- **CloudWatch Alarms**: 메트릭 임계값 기반 알림으로 이벤트 기반 워크플로우에 부적합

### 실전 적용

- **실시간 로그 분석**: S3에 스트리밍되는 로그 데이터를 Crawler가 감지하면 즉시 ETL 처리
- **데이터 품질 검증**: 새로운 데이터셋이 추가될 때마다 자동으로 품질 검사 파이프라인 실행
- **멀티 소스 데이터 통합**: 다양한 소스에서 들어오는 데이터를 통합 스키마로 변환

## 📝 관련 문제

**Question:** A Data Scientist is building a data lake solution in Amazon S3 for storing multiple types of data. The data lake must support serverless metadata store and event-driven ETL pipelines. Which combination of AWS Services should the Scientist use? (Select TWO.)

**Options:**

- A) Configure a CloudWatch alarm that triggers a Lambda function to start an AWS Batch job when a crawler run is done
- B) Configure a CloudWatch event that triggers a Lambda function to start a Glue ETL job when a crawler run is done
- C) Configure a CloudWatch alarm that triggers a Lambda function to start a Glue ETL job when a crawler run is done
- D) Extract the S3 data using AWS Glue crawlers and create metadata tables using an Apache Hive metastore running on Amazon EMR
- E) Extract the S3 data using AWS Glue crawlers and create metadata tables using AWS Glue Data Catalog

**정답: B, E) CloudWatch Events + Lambda → Glue ETL, AWS Glue Data Catalog**

💡 추가 설명:

- **옵션 A, C** - CloudWatch Alarm은 메트릭 기반 모니터링으로 이벤트 기반 트리거에 부적합
- **옵션 A** - AWS Batch는 컨테이너 기반 배치 처리로 ETL 전용 Glue보다 복잡함
- **옵션 D** - EMR의 Hive 메타스토어는 클러스터 관리가 필요하여 서버리스 요구사항 위배