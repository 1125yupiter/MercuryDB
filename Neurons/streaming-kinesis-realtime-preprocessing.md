---

title: streaming-kinesis-realtime-preprocessing
created: 2025-08-18
modified: 2025-08-18
tags:
- service/kinesis
- constraint/real-time
- usecase/fraud-detection
- technique/stream-processing
- problem/data-preprocessing
aliases: ["kinesis-analytics", "stream-preprocessing", "realtime-etl"]

---

# 스트리밍 데이터 실시간 전처리를 위한 Kinesis Data Analytics 활용

## 🎯 핵심 포인트

대용량 스트리밍 거래 데이터에서 불필요한 컬럼이 많고 실시간 ML 모델 적용이 필요한 경우, Kinesis Data Streams와 Kinesis Data Analytics를 활용하여 저장 전 실시간 데이터 필터링 및 변환을 수행할 수 있다.

## 📝 설명

### Kinesis Data Analytics가 스트리밍 데이터 전처리에 적합한 이유

Kinesis Data Analytics는 실시간 스트림 처리를 위한 완전 관리형 서비스로, SQL 기반의 실시간 데이터 변환과 필터링을 제공합니다. 특히 금융 거래 데이터처럼 수백 개의 컬럼 중 일부만 필요한 상황에서, 불필요한 데이터를 저장 전에 제거하여 스토리지 비용을 절약하고 처리 효율성을 극대화할 수 있습니다.

핵심 특징:
- **실시간 SQL 변환**: 스트리밍 데이터에 대해 SELECT, WHERE, JOIN 등 표준 SQL 구문 적용
- **즉시 필터링**: 저장 전 불필요한 컬럼과 레코드 제거
- **자동 스케일링**: 데이터 볼륨에 따른 자동 처리 용량 조정
- **다양한 출력**: S3, Kinesis Data Streams, Lambda 등 다중 출력 대상 지원

### 아키텍처 플로우

```
원본 거래 데이터 (수백 컬럼) → Kinesis Data Streams → 
Kinesis Data Analytics (SQL 기반 필터링/변환) → 
정제된 데이터 (필요 컬럼만) → S3/DynamoDB → ML 모델 (사기 탐지)
```

### Trade-offs 고려사항

**Kinesis Data Analytics 장점**:
- 저장 비용 최소화 (불필요한 데이터 사전 제거)
- 실시간 처리로 지연시간 최소
- SQL 기반으로 학습 곡선 낮음
- 완전 관리형으로 인프라 관리 불필요

**Kinesis Data Analytics 단점**:
- 복잡한 변환 로직 구현 제약
- 비용이 처리량에 비례하여 증가
- SQL로 표현하기 어려운 변환은 Lambda 함수 필요

**AWS Glue ETL (S3 저장 후) 장점**:
- 복잡한 변환 로직 지원 (Python/Scala)
- 스키마 추론 및 데이터 카탈로그 통합
- 대용량 배치 처리에 최적화

**AWS Glue ETL (S3 저장 후) 단점**:
- 모든 원본 데이터를 먼저 저장해야 함 (스토리지 낭비)
- 배치 처리로 인한 지연시간 발생
- 실시간 요구사항에 부적합

**Glue DataBrew 장점**:
- 시각적 데이터 준비 인터페이스
- 코드 없는 데이터 변환
- 다양한 데이터 품질 프로파일링

**Glue DataBrew 단점**:
- 배치 처리 중심 (실시간 스트림 부적합)
- 모든 데이터 저장 후 처리 (스토리지 비효율)
- 스트리밍 데이터 소스 미지원

## 🔍 주요개념

### 스트림 처리 vs 배치 처리

- **스트림 처리 (Kinesis Analytics)**: 데이터 도착 즉시 처리, 저장 전 변환으로 스토리지 효율성 극대화
- **배치 처리 (Glue ETL/DataBrew)**: 일정량 축적 후 처리, 복잡한 변환 지원하지만 지연시간과 스토리지 비용 증가

### 실전 적용

- **실시간 사기 탐지**: 거래 데이터에서 의심 패턴을 즉시 식별하여 차단
- **고객 행동 분석**: 웹/앱 이벤트 스트림에서 핵심 지표만 추출하여 개인화 서비스 제공
- **IoT 센서 데이터**: 수천 개 센서에서 오는 데이터 중 임계값 초과 항목만 필터링하여 알람 시스템 연동

## 📝 관련 문제

**Question:** A financial analytics firm receives continuous streams of transaction data, each record populated with hundreds of columns. The firm faces a challenge: a significant portion of this data is irrelevant to their machine learning model, which aims to predict transaction fraud. Additionally, certain relevant columns require minor transformations or concatenation. What is the most straightforward and storage-efficient method to preprocess this data upon its arrival?

**Options:**

- A) Process the incoming data with the spark-sagemaker library prior to its utilization in training the model
- B) Collect all the data into S3, then utilize AWS Glue DataBrew to selectively prune unnecessary columns as part of the data preparation phase before model training
- C) Trigger a Glue ETL job to transform the streaming data once it is stored in S3
- D) Transform the incoming data stream using Kinesis Data Streams and Amazon Kinesis Data Analytics

**정답: D) Transform the incoming data stream using Kinesis Data Streams and Amazon Kinesis Data Analytics**

💡 추가 설명:

- **A) Spark-SageMaker 라이브러리** - 모델 훈련 시점의 전처리로, 이미 모든 데이터가 저장된 후 처리되어 스토리지 효율성 부족
- **B) S3 저장 후 Glue DataBrew** - 불필요한 컬럼까지 모두 저장 후 제거하는 방식으로 스토리지 비용 낭비, 실시간 처리 요구사항에 부적합
- **C) S3 저장 후 Glue ETL** - 배치 처리 방식으로 실시간성 부족, 원본 데이터 전체 저장으로 인한 비효율성