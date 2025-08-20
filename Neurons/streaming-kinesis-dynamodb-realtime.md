---

title: streaming-kinesis-dynamodb-realtime
created: 2025-08-20
modified: 2025-08-20
tags:
- service/kinesis-firehose
- service/dynamodb
- constraint/real-time
- usecase/machine-learning
- streaming/data-collection
aliases: ["streaming-data", "kinesis-firehose", "realtime-ml"]

---

# 실시간 스트리밍 데이터 수집 및 처리를 위한 AWS 서비스 선택

## 🎯 핵심 포인트

머신러닝 목적으로 스트리밍 데이터를 실시간으로 수집하고 처리해야 하는 경우, Amazon Kinesis Data Firehose와 Amazon DynamoDB를 조합하여 end-to-end 데이터 파이프라인을 구성할 수 있다.

## 📝 설명

### Amazon Kinesis Data Firehose가 실시간 스트리밍 데이터 처리에 적합한 이유

Amazon Kinesis Data Firehose는 완전 관리형 서비스로 실시간 스트리밍 데이터를 수집, 변환, 전송하는 기능을 제공한다. 서버리스 아키텍처로 자동 스케일링되며, 데이터를 S3, Redshift, Elasticsearch 등 다양한 목적지로 안정적으로 전송할 수 있다. 기본적인 데이터 변환 기능과 압축, 암호화 기능을 내장하고 있어 ML 파이프라인의 첫 단계로 활용하기에 적합하다.

### Amazon DynamoDB가 실시간 데이터 처리에 적합한 이유

Amazon DynamoDB는 NoSQL 데이터베이스로 밀리초 단위의 낮은 지연시간으로 대용량 데이터를 읽고 쓸 수 있다. 실시간 데이터 수집이 가능하며, DynamoDB Streams를 통해 데이터 변경사항을 실시간으로 캡처하여 다운스트림 처리를 트리거할 수 있다. 자동 스케일링과 글로벌 테이블 기능으로 고가용성을 보장한다.

### 아키텍처 플로우

```
Data Sources → Kinesis Data Firehose → [Transform] → S3/Redshift/ES
     ↓
Real-time Apps → DynamoDB → DynamoDB Streams → Lambda/Analytics
     ↓
ML Training & Inference Pipeline
```

### Trade-offs 고려사항

**Amazon Kinesis Data Firehose 장점**:
- 완전 관리형 서비스로 운영 부담 최소화
- 자동 스케일링 및 내결함성 제공
- 다양한 목적지 지원 (S3, Redshift, Elasticsearch 등)
- 기본적인 데이터 변환 기능 내장

**Amazon Kinesis Data Firehose 단점**:
- 복잡한 실시간 처리 로직에는 제한적
- 데이터 전송에 최소 60초 지연 발생 가능
- 변환 로직의 커스터마이징 제약

**Amazon DynamoDB 장점**:
- 매우 낮은 지연시간 (밀리초 단위)
- 자동 스케일링으로 트래픽 급증 대응
- DynamoDB Streams로 실시간 변경 감지
- 완전 관리형으로 운영 복잡성 최소화

**Amazon DynamoDB 단점**:
- 복잡한 쿼리나 조인 연산에 제약
- 스토리지 비용이 상대적으로 높음
- 스키마 변경의 복잡성

**AWS Glue 장점**:
- Apache Spark 기반의 강력한 ETL 기능
- 스트리밍 ETL 작업 지원 (Structured Streaming)
- 서버리스로 인프라 관리 불필요

**AWS Glue 단점**:
- 데이터 수집(collection) 기능이 핵심이 아님
- 다른 서비스에서 수집된 데이터를 처리하는 중간 단계 역할
- 실시간 데이터 ingestion에 직접적이지 않음

## 🔍 주요개념

### 실시간 vs 준실시간 처리

- **실시간 처리**: 밀리초~초 단위 지연시간으로 즉시 처리 (DynamoDB, Kinesis Data Streams)
- **준실시간 처리**: 분 단위 지연을 허용하는 배치 처리 (Kinesis Data Firehose, Glue Streaming)

### 데이터 수집 vs 데이터 처리

- **데이터 수집**: 외부 소스에서 데이터를 받아들이는 단계 (Kinesis, DynamoDB 직접 쓰기)
- **데이터 처리**: 수집된 데이터를 변환, 분석하는 단계 (Glue, Lambda, EMR)

### 실전 적용

- **IoT 센서 데이터 수집**: Kinesis Data Firehose로 센서 데이터를 S3에 저장하고 실시간 분석
- **실시간 추천 시스템**: DynamoDB에 사용자 행동 데이터를 저장하고 즉시 추천 알고리즘에 활용
- **실시간 이상 탐지**: 스트리밍 데이터를 DynamoDB에 저장하면서 동시에 ML 모델로 이상 패턴 감지

## 📝 관련 문제

**Question:** Which of the following AWS services can be used to efficiently collect and process streaming data in real-time for machine learning purposes? (Select 2)

**Options:**

- A) Amazon Kinesis Data Firehose
- B) Amazon DynamoDB  
- C) AWS Data Pipeline
- D) AWS Glue
- E) Amazon SNS

**정답: A, B) Amazon Kinesis Data Firehose, Amazon DynamoDB**

💡 추가 설명:

- **AWS Data Pipeline** - 배치 데이터 처리를 위한 서비스로 실시간 스트리밍 처리에 부적합
- **AWS Glue** - ETL 서비스로 스트리밍 처리 기능이 있지만 데이터 수집(collection)이 핵심 기능이 아님
- **Amazon SNS** - 메시징 및 알림 서비스로 데이터 수집 및 처리 목적이 아님