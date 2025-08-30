---

title: streaming-kinesis-realtime-analytics-ml
created: 2025-08-21
modified: 2025-08-21
tags:
- service/kinesis-data-firehose
- constraint/real-time
- usecase/ml-analytics
- service/emr
- technique/spark-streaming
aliases: ["kinesis-firehose", "spark-streaming", "realtime-processing"]

---

# AWS 실시간 스트리밍 데이터 처리: Kinesis vs EMR Spark Streaming

## 🎯 핵심 포인트

여러 소스에서 스트리밍 데이터를 수집하고 머신러닝 애플리케이션을 위한 거의 실시간 분석이 필요한 경우, **Amazon Kinesis Data Firehose**가 가장 적합한 완전 관리형 솔루션이지만, 복잡한 실시간 처리 로직이 필요한 경우에는 **EMR + Spark Streaming**을 활용할 수 있다.

## 📝 설명

### Amazon Kinesis Data Firehose가 실시간 스트리밍에 적합한 이유

Amazon Kinesis Data Firehose는 완전 관리형 실시간 데이터 전송 서비스로, 여러 소스에서 스트리밍 데이터를 자동으로 수집하고 변환하여 데이터 레이크, 데이터 웨어하우스, 분석 서비스로 안정적으로 전송합니다. 서버리스 아키텍처로 자동 스케일링되며, 데이터 압축, 암호화, 포맷 변환 기능을 내장하고 있어 운영 오버헤드 없이 거의 실시간 처리가 가능합니다.

### EMR + Spark Streaming의 실시간 처리 능력

Amazon EMR에서 실행되는 Apache Spark Streaming은 마이크로 배치 방식으로 초 단위 또는 밀리초 단위의 준실시간 처리를 제공합니다. Structured Streaming을 통해 더욱 낮은 지연시간을 달성할 수 있으며, 복잡한 윈도우 연산, 조인, 집계 등의 고급 분석 작업이 가능합니다. Kafka, Kinesis, TCP 소켓 등 다양한 소스에서 데이터를 실시간으로 수집하여 처리할 수 있습니다.

### 아키텍처 플로우

```
데이터 소스들 → Kinesis Data Firehose → 변환/압축 → S3/Redshift/ES
     ↓
   Kinesis Data Streams ← EMR Spark Streaming ← 복잡한 실시간 분석
     ↓                           ↓
   Lambda 처리           → 실시간 ML 추론 → 결과 저장
```

### Trade-offs 고려사항

**Kinesis Data Firehose 장점**:
- 완전 관리형 서비스로 운영 복잡도 최소화
- 자동 스케일링과 내장된 오류 처리
- 다양한 AWS 서비스와의 네이티브 통합
- 비용 효율적인 사용량 기반 과금

**Kinesis Data Firehose 단점**:
- 복잡한 실시간 분석 로직 구현에 제한
- 사전 정의된 변환 기능에만 의존
- 커스텀 처리 로직 구현의 어려움

**EMR + Spark Streaming 장점**:
- 복잡한 실시간 분석과 ML 파이프라인 구현 가능
- 다양한 데이터 소스와 포맷 지원
- 유연한 커스텀 로직 구현
- 대용량 데이터 처리에 최적화

**EMR + Spark Streaming 단점**:
- 클러스터 관리와 운영 복잡도 증가
- 높은 인프라 비용과 리소스 관리 필요
- 개발 및 유지보수 리소스 필요

## 🔍 주요개념

### 실시간 처리 방식 비교

- **Kinesis Data Firehose**: 스트림 버퍼링 방식으로 60초 또는 1MB 단위로 배치 전송
- **Spark Streaming**: 마이크로 배치 방식으로 초 단위 처리 간격 설정 가능
- **Structured Streaming**: 연속 처리 모델로 더욱 낮은 지연시간 달성

### 실전 적용

- **로그 분석**: 웹 애플리케이션 로그를 실시간으로 수집하여 이상 탐지 및 성능 모니터링
- **IoT 데이터 처리**: 센서 데이터를 실시간으로 수집하여 예측 유지보수 및 알람 시스템 구축
- **금융 사기 탐지**: 거래 데이터를 실시간으로 분석하여 사기 패턴 탐지 및 즉시 차단

## 📝 관련 문제

**Question:** A company needs to collect streaming data from multiple IoT sensors and process it for real-time fraud detection in financial transactions. The solution must provide near-real-time analytics for machine learning applications with minimal operational overhead. Which AWS service combination would be most appropriate?

**Options:**

- A) Amazon Redshift + AWS Data Pipeline
- B) Amazon Kinesis Data Firehose + Amazon S3 + Amazon SageMaker
- C) Amazon EMR + Apache Spark Streaming + Amazon DynamoDB
- D) AWS Glue + Amazon Athena + Amazon QuickSight
- E) Amazon Kinesis Data Streams + AWS Lambda + Amazon RDS

**정답: B) Amazon Kinesis Data Firehose + Amazon S3 + Amazon SageMaker**

💡 추가 설명:

- **A) Amazon Redshift + AWS Data Pipeline** - 배치 처리용 솔루션으로 실시간 스트리밍에 부적합
- **C) Amazon EMR + Apache Spark Streaming** - 실시간 처리 가능하지만 운영 복잡도가 높고 완전 관리형이 아님
- **D) AWS Glue + Amazon Athena** - ETL과 쿼리 서비스로 실시간 스트리밍 데이터 수집에 부적합
- **E) Amazon Kinesis Data Streams + AWS Lambda** - 실시간 처리 가능하지만 ML 분석에는 Firehose + SageMaker 조합이 더 적합