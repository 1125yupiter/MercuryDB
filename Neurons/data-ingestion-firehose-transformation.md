---
title: data-ingestion-firehose-transformation
created: 2025-08-17
modified: 2025-08-17
tags:

  - service/firehose
  - service/s3
  - service/athena
  - constraint/real-time
  - usecase/data-transformation
aliases: ["kinesis firehose", "data delivery service"]
---

# 실시간 데이터 수집 및 변환을 위한 Amazon Kinesis Data Firehose

## 🎯 핵심 포인트

제조 회사에서 S3에 실시간으로 데이터를 수집하고 Athena로 분석하기 위해, 스트리밍 데이터를 Apache Parquet 형식으로 변환해야 하는 경우 **Amazon Kinesis Data Firehose**를 사용할 수 있습니다.

-----

## 📝 설명

### Amazon Data Firehose가 실시간 데이터 수집 및 변환에 적합한 이유

**Amazon Kinesis Data Firehose**는 실시간 스트리밍 데이터를 특정 목적지로 손쉽게 전송하고, 이 과정에서 데이터를 변환할 수 있는 완전 관리형 서비스입니다. 사용자는 별도의 서버나 애플리케이션을 관리할 필요 없이 데이터를 수집하고, 원하는 형태로 가공하여 S3와 같은 데이터 레이크에 저장할 수 있습니다.

**핵심 특징 및 작동 방식**:

  * **자동 스케일링**: 데이터 처리량이 변동하더라도 자동으로 처리 용량을 조절하여 안정적인 서비스를 제공합니다.
  * **통합 변환 기능**: AWS Lambda 함수를 사용하여 수신 데이터를 원하는 형식으로 변환하는 기능을 제공합니다. 이 기능을 통해 JSON, CSV 등의 데이터를 Parquet와 같은 분석에 최적화된 포맷으로 변경할 수 있습니다.
  * **배치 및 압축**: 데이터를 S3에 저장하기 전에 일정 크기 또는 시간 단위로 버퍼링하고, gzip이나 snappy 같은 압축 포맷을 적용하여 스토리지 비용을 절감하고 쿼리 성능을 향상시킬 수 있습니다.
  * **다양한 목적지**: Amazon S3 외에도 Amazon Redshift, OpenSearch Service 등으로 데이터를 전송할 수 있습니다.

### 아키텍처 플로우

```
Data Source (Manufacturing IoT Sensors)
      ↓ (Real-time Streaming Data)
Amazon Kinesis Data Firehose
      ↓ (Invoke AWS Lambda for Transformation)
AWS Lambda (Data Transformation: JSON -> Parquet)
      ↓ (Transformed, Buffered & Compressed Data)
Amazon S3 (Data Lake)
      ↓ (SQL Queries for Analysis)
Amazon Athena
```

-----

## Trade-offs 고려사항

**Amazon Kinesis Data Firehose 장점**:

  * **운영 용이성**: 완전 관리형 서비스로, 서버 관리나 인프라 확장/축소에 대한 걱정이 없습니다.
  * **비용 효율성**: 데이터 처리량에 따라 비용을 지불하므로, 사용한 만큼만 지불합니다.
  * **간편한 통합**: AWS Lambda와의 연동을 통해 복잡한 데이터 변환 로직을 쉽게 구현할 수 있습니다.
  * **데이터 분석 최적화**: Parquet와 같은 압축 및 컬럼 기반 형식으로 데이터를 저장하여 Athena와 같은 분석 도구의 쿼리 성능을 극대화할 수 있습니다.

**Amazon Kinesis Data Firehose 단점**:

  * **세밀한 제어 부족**: Kinesis Data Streams와 비교하여 버퍼링 및 데이터 전송 간격을 세밀하게 제어하기 어렵습니다.
  * **최소 지연 시간**: 버퍼링 정책으로 인해 Kinesis Data Streams보다 약간의 지연 시간이 발생할 수 있습니다.

**Amazon Kinesis Data Streams 장점**:

  * **세밀한 제어**: 스트리밍 데이터의 각 레코드를 직접 제어할 수 있어 초저지연 애플리케이션에 적합합니다.
  * **높은 유연성**: 다양한 소비자(Consumer)를 연결하여 여러 애플리케이션에서 동일한 데이터를 실시간으로 사용할 수 있습니다.

**Amazon Kinesis Data Streams 단점**:

  * **운영 복잡성**: 샤드(shard) 관리 등 인프라를 직접 운영해야 하므로 운영 난이도가 높습니다.
  * **추가 비용 및 개발**: 데이터 변환 및 S3 저장을 위해서는 별도의 Lambda 또는 Kinesis Data Analytics 애플리케이션을 구성해야 합니다.

-----

## 🔍 주요개념

### 실시간 데이터 처리 서비스 비교

  * **Amazon Data Firehose**: 데이터 수집, 변환, 그리고 목적지로의 전송을 통합하여 제공하는 서비스. ETL(Extract, Transform, Load) 파이프라인의 **'Load'** 단계에 특화되어 있으며, 서버리스 아키텍처를 지향합니다.
  * **Amazon Kinesis Data Streams**: 실시간 데이터 스트림을 캡처, 처리 및 분석하는 서비스. **'Extract'** 단계에 특화되어 있으며, 초저지연 처리가 필요한 애플리케이션의 핵심 구성 요소로 사용됩니다.

### 실전 적용

  * **제조업 IoT 데이터 수집**: 제조 라인의 IoT 센서에서 발생하는 스트리밍 데이터를 Kinesis Data Firehose로 수집하고, 데이터 형식을 Parquet으로 변환하여 S3에 저장한 후 Athena로 품질 관리 및 생산 효율 분석을 수행합니다.
  * **웹 클릭스트림 데이터 분석**: 웹사이트 사용자의 클릭 이벤트를 실시간으로 수집하여 Firehose로 전송하고, 사용 패턴 분석을 위해 S3에 저장합니다. 이후 Athena를 사용하여 사용자 행동을 분석합니다.
  * **로그 데이터 통합**: 여러 서버에서 발생하는 애플리케이션 로그를 Kinesis Data Firehose로 통합하여 S3에 저장하고, Athena로 장애 원인 분석 및 성능 모니터링을 진행합니다.

-----

## 📝 관련 문제

**Question:** A manufacturing company wants to aggregate data in Amazon S3 and analyze it using Amazon Athena. The company needs a solution that can both ingest and transform streaming data into Apache Parquet format.
Which AWS Service meets the requirements?

**Options:**

  * A) Amazon Kinesis Data Streams
  * B) AWS Database Migration Service
  * C) Amazon Data Firehose
  * D) AWS Batch
  * E) None of the above

**정답: C) Amazon Data Firehose**

💡 추가 설명:

  * **오답 옵션 A) Amazon Kinesis Data Streams** - 실시간 데이터 수집에는 적합하나, 데이터 변환 및 S3 저장을 위해서는 별도의 추가 개발이 필요합니다.
  * **오답 옵션 B) AWS Database Migration Service** - 데이터베이스 마이그레이션을 위한 서비스로, 실시간 스트리밍 데이터 수집 및 변환에는 부적합합니다.
  * **오답 옵션 D) AWS Batch** - 대규모 배치 처리 작업을 위한 서비스로, 실시간 스트리밍 데이터 처리에는 적합하지 않습니다.