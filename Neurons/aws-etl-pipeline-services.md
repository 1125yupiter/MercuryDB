---

title: aws-etl-pipeline-services
created: 2025-08-19
modified: 2025-08-19
tags:
- service/glue
- service/kinesis-data-analytics
- constraint/real-time
- constraint/batch
- usecase/data-processing
aliases: ["etl", "data-pipeline", "stream-processing"]

---

# AWS ETL 서비스와 데이터 파이프라인 구성

## 🎯 핵심 포인트

여러 데이터 소스를 처리하는 ML 파이프라인을 구축하는 경우, 배치 처리에서는 AWS Glue를, 실시간 스트림 처리에서는 Kinesis Data Analytics를 사용할 수 있다.

## 📝 설명

### AWS Glue가 배치 ETL에 적합한 이유

AWS Glue는 완전 관리형 ETL 서비스로, 다양한 데이터 소스(S3, RDS, Redshift, DynamoDB 등)에서 데이터를 추출하고 변환하여 목적지로 로드하는 작업을 자동화합니다. Python/Scala로 커스텀 ETL 작업이 가능하며, 데이터 카탈로그를 자동 생성하고 서버리스 환경에서 스케일링을 자동으로 처리합니다.

### Kinesis Data Analytics가 실시간 ETL에 적합한 이유

이름에 "Analytics"가 포함되어 있어 분석 도구로 오해받기 쉽지만, 실제로는 강력한 실시간 스트림 처리 엔진입니다. SQL 또는 Java를 사용하여 실시간으로 들어오는 데이터를 필터링, 집계, 변환하고 다른 서비스로 전송할 수 있습니다.

### 아키텍처 플로우

**배치 처리:**
```
Data Sources (S3, RDS, DynamoDB)
    ↓
AWS Glue ETL Job
    ↓
Transformed Data (S3, Redshift)
    ↓
ML Training/Analysis
```

**실시간 처리:**
```
Real-time Data Streams
    ↓
Kinesis Data Analytics (SQL/Java)
    ↓ (filtering, aggregation, transformation)
Processed Stream Data
    ↓
Target Services (S3, Lambda, Kinesis Firehose)
```

**파이프라인 오케스트레이션:**
```
AWS Step Functions
    ↓
Multiple Services Coordination
    ↓
Error Handling & Retry Logic
```

### Trade-offs 고려사항

**AWS Glue 장점:**
- 완전 관리형 서비스로 인프라 관리 불필요
- 다양한 데이터 소스 지원
- 자동 스케일링 및 비용 효율성
- 데이터 카탈로그 자동 생성

**AWS Glue 단점:**
- 실시간 처리 불가능 (배치 전용)
- 초기 작업 시작 시간이 상대적으로 길음
- 복잡한 로직에는 제한적

**Kinesis Data Analytics 장점:**
- 진정한 실시간 처리 가능
- SQL로 간편한 스트림 처리
- 자동 스케일링 지원
- 실시간 집계 및 윈도우 함수 지원

**Kinesis Data Analytics 단점:**
- 배치 데이터 처리에는 부적합
- 스트리밍 데이터에만 특화
- 복잡한 변환 로직에는 제한적

## 🔍 주요개념

### 처리 방식 비교

- **배치 처리**: 데이터를 축적한 후 정해진 시간에 일괄 처리 (AWS Glue, EMR)
- **실시간 처리**: 데이터가 들어오는 즉시 처리 및 출력 (Kinesis Data Analytics)
- **니어 실시간**: 작은 배치 단위로 빠른 주기 처리 (Lambda, Kinesis Data Firehose)

### 파이프라인 오케스트레이션

- **AWS Step Functions**: 서버리스 워크플로우 조정, JSON으로 워크플로우 정의
- **AWS Data Pipeline**: 레거시 배치 워크플로우 서비스 (현재 비권장)
- **SageMaker Pipelines**: ML 전용 파이프라인 (전처리 → 훈련 → 배포)

### 실전 적용

- **전자상거래 추천 시스템**: 실시간 클릭 스트림을 Kinesis Data Analytics로 처리하여 즉시 개인화 추천 생성
- **금융 이상 거래 탐지**: 배치로 하루치 거래 데이터를 Glue로 전처리하고, 실시간 거래는 Kinesis로 즉시 분석
- **IoT 센서 데이터 분석**: 실시간 센서 데이터를 Kinesis로 집계하고, 일간 보고서는 Glue로 배치 처리

## 📝 관련 문제

**Question:** You are building an AWS Machine Learning pipeline and need to process data from a variety of sources. Which of the following AWS services can be used for ETL (Extract, Transform, Load) and data processing?

**Options:**

- A) AWS Lambda
- B) AWS Glue  
- C) AWS Step Functions
- D) Amazon S3
- E) Amazon Kinesis Data Analytics

**정답: B) AWS Glue, E) Amazon Kinesis Data Analytics**

💡 추가 설명:

- **AWS Lambda** - 간단한 데이터 변환은 가능하지만 15분 실행 제한으로 대용량 ETL에 부적합
- **AWS Step Functions** - ETL 워크플로우 오케스트레이션 역할이지 직접적인 ETL 기능은 없음
- **Amazon S3** - 데이터 저장소 역할로 ETL 처리 기능 자체는 제공하지 않음
- **AWS Glue** - 완전 관리형 ETL 서비스로 배치 데이터 처리에 최적화
- **Kinesis Data Analytics** - 실시간 스트림 데이터의 ETL 처리에 특화된 서비스