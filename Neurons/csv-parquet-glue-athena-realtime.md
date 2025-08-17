---

title: csv-parquet-glue-athena-realtime
created: 2025-08-17
modified: 2025-08-17
tags:
- service/kinesis-firehose
- service/glue-streaming
- format/csv-parquet
- constraint/realtime
- usecase/athena-analytics
aliases: ["csv-to-parquet", "firehose-limitations", "glue-streaming-etl"]

---

# AWS Glue Streaming ETL이 CSV to Parquet 실시간 변환에 적합한 이유

## 🎯 핵심 포인트

온프레미스에서 생성되는 CSV 파일을 실시간으로 Parquet 형식으로 변환하여 S3에 저장하고 Athena로 분석해야 하는 경우, AWS Glue Streaming ETL을 사용하면 최소한의 구성 오버헤드로 스키마 추론부터 Data Catalog 업데이트까지 자동화할 수 있다.

## 📝 설명

### AWS Glue Streaming ETL이 CSV to Parquet 변환에 적합한 이유

**Kinesis Data Firehose의 핵심 제약사항**: Firehose는 **구조화된 스키마 정보가 포함된 데이터**만 직접 Parquet으로 변환할 수 있다. JSON은 자체적으로 데이터 타입 정보를 포함하고 있어 자동 스키마 추론이 가능하지만, CSV는 모든 값이 문자열로 처리되어 별도의 스키마 정의가 필요하다.

**Glue Streaming ETL의 장점**: 서버리스 환경에서 복잡한 데이터 변환 로직을 실행할 수 있으며, CSV 파싱부터 데이터 타입 추론, Parquet 최적화까지 모든 과정을 자동화한다. 특히 Athena 분석을 위한 파티셔닝, Data Catalog 업데이트, 쿼리 최적화까지 고려한 파이프라인을 구성할 수 있다.

### 아키텍처 플로우

```
온프레미스 CSV 파일 생성
↓
Kinesis Data Streams (실시간 수집)
↓
Glue Streaming ETL
├── CSV 파싱 및 스키마 추론
├── 데이터 타입 변환
├── 자동 파티셔닝 (년/월/일)
├── Parquet 최적화 (압축, 컬럼형 저장)
└── Data Catalog 메타데이터 업데이트
↓
S3 (Athena 최적화된 Parquet 파일)
↓
Amazon Athena (고성능 쿼리)
```

### Trade-offs 고려사항

**Glue Streaming ETL 장점**:
- 서버리스로 인프라 관리 오버헤드 최소화
- 복잡한 데이터 변환 로직 지원 (CSV 스키마 추론)
- Athena 최적화 자동 적용 (파티셔닝, Data Catalog 연동)
- Apache Spark 기반의 강력한 병렬 처리 능력

**Glue Streaming ETL 단점**:
- 초기 설정 시 스키마 정의 필요할 수 있음
- Spark 기반으로 상대적으로 높은 리소스 사용

**Kinesis Data Firehose 장점**:
- 설정이 매우 간단
- JSON to Parquet는 완벽하게 지원

**Kinesis Data Firehose 단점**:
- CSV to Parquet 직접 변환 불가능
- 복잡한 데이터 변환 로직 제한적
- Athena 최적화 기능 부족

## 🔍 주요개념

### 데이터 형식별 Firehose 변환 지원

- **JSON → Parquet/ORC**: ✅ 완전 지원 (자체 스키마 정보 포함)
- **CSV → Parquet/ORC**: ❌ 직접 변환 불가 (스키마 정보 부족)
- **XML → Parquet/ORC**: ❌ 구조가 너무 복잡
- **Avro → Parquet/ORC**: ❌ 직접 지원 안함

### Firehose 변환의 "골디락스 존"

Firehose는 **너무 단순하지도, 너무 복잡하지도 않은** JSON 형식에 최적화되어 있다:
- **너무 단순 (CSV)**: 데이터 타입 정보 없음
- **적당함 (JSON)**: 구조화 + 타입 정보 포함
- **너무 복잡 (XML)**: 중첩 구조로 컬럼형 매핑 어려움

### 실전 적용

- **실시간 로그 분석**: 애플리케이션 로그 CSV를 실시간으로 Parquet 변환하여 성능 모니터링
- **IoT 센서 데이터**: 제조업 센서 CSV 데이터를 파티셔닝하여 시계열 분석 최적화
- **금융 거래 데이터**: 실시간 거래 CSV를 Parquet으로 변환하여 이상 거래 패턴 분석

## 📝 관련 문제

**Question:** A Data Engineer is designing a solution for customer data analysis using Amazon Athena. An on-premises application produces the data as CSV files in near real-time. The Engineer needs to convert the data to Apache Parquet format before saving it on an Amazon S3 bucket. Which method provides the LEAST configuration overhead?

**Options:**

- A) Use Amazon Kinesis Data Streams to consume customer data. Create a streaming ETL job in AWS Glue to convert data into Apache Parquet.
- B) Configure an Amazon EMR cluster with Apache Spark Structured Streaming to consume and transform the customer data into Apache Parquet.
- C) Configure an Amazon EC2 instance with Apache Kafka to consume the customer data. Export the data to the S3 bucket in Parquet format with Kafka Connect S3 sink connector.
- D) Use Amazon Kinesis Data Streams to ingest customer data and configure a Firehose stream as a consumer to convert the data into Apache Parquet.

**정답: A) Use Amazon Kinesis Data Streams to consume customer data. Create a streaming ETL job in AWS Glue to convert data into Apache Parquet.**

💡 추가 설명:

- **옵션 B (EMR 클러스터)** - 클러스터 설정, 관리, 확장성 구성 등 많은 인프라 관리 오버헤드 발생
- **옵션 C (EC2 + Kafka)** - EC2 인스턴스 관리, Kafka 설정, Kafka Connect 구성 등 가장 복잡한 설정 작업 필요
- **옵션 D (Firehose)** - CSV를 직접 Parquet으로 변환하는 기능 제한적, JSON만 직접 변환 지원
- **핵심 이유**: Athena 분석이 최종 목적이므로 파티셔닝, Data Catalog 연동, 쿼리 최적화까지 고려한 Glue Streaming ETL이 전체 파이프라인 관점에서 가장 효율적