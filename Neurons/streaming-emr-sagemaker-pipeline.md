---

title: streaming-emr-sagemaker-pipeline
created: 2025-08-20
modified: 2025-08-20
tags:
- service/emr
- service/sagemaker
- constraint/real-time
- usecase/streaming-ml
- architecture/distributed
aliases: ["EMR", "elastic-mapreduce", "streaming-ml"]

---

# 실시간 스트리밍 데이터를 위한 EMR + SageMaker ML 파이프라인

## 🎯 핵심 포인트

소셜 미디어와 같은 대용량 실시간 스트리밍 데이터를 ML 파이프라인으로 처리해야 하는 경우, Kinesis Data Streams에서 EMR로 전처리하고 SageMaker로 모델 훈련 및 배포할 수 있다.

## 📝 설명

### EMR이 스트리밍 ML 파이프라인에 적합한 이유

Amazon EMR(Elastic MapReduce)은 Apache Spark를 활용한 분산 처리를 통해 대용량 스트리밍 데이터를 효율적으로 처리할 수 있는 관리형 서비스입니다. Spark Structured Streaming을 사용하여 마이크로 배치 방식으로 준실시간 처리를 수행하며, Kinesis Data Streams와 네이티브 통합을 제공합니다.

### 아키텍처 플로우

```
Kinesis Data Streams → EMR Spark Cluster → SageMaker → REST API Endpoint
      (실시간 수집)      (Structured Streaming)  (모델 훈련)    (예측 서빙)
                           ↓
                    마이크로 배치 처리
                    (1-10초 간격)
                           ↓
                    전처리된 데이터 → S3
```

### 실시간 스트리밍 처리 방식

EMR에서는 **Spark Structured Streaming**을 통해 스트리밍 데이터를 처리합니다:

```python
# Kinesis에서 스트리밍 데이터 읽기
df = spark.readStream \
    .format("kinesis") \
    .option("streamName", "social-media-stream") \
    .option("region", "us-east-1") \
    .load()

# 실시간 전처리 파이프라인
processed_df = df.select(col("data").cast("string")) \
    .select(from_json(col("data"), schema).alias("parsed")) \
    .filter(col("user_id").isNotNull())

# 마이크로 배치로 처리
query = processed_df.writeStream \
    .trigger(processingTime='10 seconds') \
    .start()
```

### Trade-offs 고려사항

**EMR + SageMaker 장점**:
- 완전 관리형 서비스로 운영 부담 최소화
- 자동 스케일링으로 대용량 데이터 처리 가능
- Kinesis와 네이티브 통합
- Apache Spark 클러스터 자동 설정 및 관리
- SageMaker의 분산 훈련 및 자동 배포 기능

**EMR + SageMaker 단점**:
- 진정한 실시간(millisecond)이 아닌 준실시간(seconds) 처리
- 관리형 서비스 사용으로 인한 추가 비용
- 커스터마이징 제약

**EC2 직접 사용 장점**:
- 완전한 제어권과 커스터마이징 가능
- 비용 최적화 가능
- 모든 오픈소스 도구 자유롭게 사용

**EC2 직접 사용 단점**:
- 복잡한 인프라 설정 및 관리 필요
- Spark 클러스터 수동 구성 및 튜닝
- 장애 복구, 모니터링 시스템 직접 구축
- 높은 운영 부담과 전문성 요구

## 🔍 주요개념

### EMR vs 직접 관리 비교

- **EMR (Managed Service)**: AWS가 클러스터 설정, 스케일링, 장애 복구를 자동 처리하는 관리형 빅데이터 처리 서비스
- **EC2 직접 관리**: 가상 머신에서 모든 소프트웨어와 인프라를 직접 설치하고 관리하는 방식

### 실시간 처리 특성

- **마이크로 배치**: Spark Streaming이 사용하는 방식으로, 연속적인 데이터를 작은 배치 단위로 나누어 처리
- **준실시간**: 몇 초에서 몇십 초의 지연시간을 가지는 처리 방식 (millisecond 단위 실시간과 구분)

### 실전 적용

- **소셜 미디어 감정 분석**: 실시간 트윗 데이터 수집 → 텍스트 전처리 → 감정 분류 모델 예측
- **IoT 센서 데이터 이상 탐지**: 센서 스트림 → 데이터 정제 → 실시간 이상 탐지 알고리즘
- **실시간 추천 시스템**: 사용자 행동 로그 → 특성 추출 → 개인화 추천 모델 서빙

## 📝 관련 문제

**Question:** A company is building a machine learning pipeline to process a high volume of real-time streaming data from a social media platform. The pipeline needs to ingest the data from Kinesis Data Streams, preprocess it with Apache Spark, train a deep learning model with TensorFlow, and serve the model predictions with a REST API. Which AWS services should they use?

**Options:**

- A) Amazon Kinesis Data Analytics, because it can preprocess the data with Apache Spark and train the model with TensorFlow in real-time
- B) Amazon EC2 (Elastic Compute Cloud), because it provides virtual machines that can run Apache Spark, TensorFlow, and the REST API
- C) Amazon Kinesis Data Firehose, because it can deliver streaming data to Amazon S3, which can be used as a data source for Apache Spark
- D) Amazon EMR (Elastic MapReduce), because it provides a managed Hadoop and Spark cluster for data processing, with SageMaker for training and API deployment
- E) Amazon Lambda with Kinesis triggers for real-time processing

**정답: D) Amazon EMR + Amazon SageMaker**

💡 추가 설명:

- **Option A (Kinesis Data Analytics)** - SQL 기반 스트림 처리 서비스로 Apache Spark나 TensorFlow 실행 불가, 커스텀 코드 제한적
- **Option B (EC2 직접 사용)** - 기술적으로 가능하지만 Spark 클러스터 설정, 스케일링, 장애 복구 등 모든 인프라 관리를 직접 해야 하는 운영 부담
- **Option C (Kinesis Data Firehose)** - 데이터 전달만 담당하는 서비스로 실제 데이터 처리 기능 없음, Spark 작업 실행 불가
- **Option E (Lambda)** - 15분 실행 시간 제한으로 딥러닝 모델 훈련 부적합, 메모리 제약으로 대용량 처리 한계

---