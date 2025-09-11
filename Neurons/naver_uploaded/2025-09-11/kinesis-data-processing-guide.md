---

title: streaming-kinesis-analytics-transform
created: 2025-08-28
modified: 2025-08-28
tags:
- service/kinesis-analytics
- service/kinesis-firehose
- constraint/real-time
- technique/data-transformation
- usecase/streaming-etl
aliases: ["kinesis-data-processing", "streaming-etl", "real-time-transform"]

---

# AWS Kinesis 스트리밍 데이터 처리: Analytics vs Firehose 선택 가이드

## 🎯 핵심 포인트

스트리밍 데이터에서 복잡한 clean, organize, transform 작업이 필요한 경우 Kinesis Data Analytics + Lambda 조합에서, 단순한 포맷 변환만 필요한 경우 Kinesis Data Firehose를 사용할 수 있다.

## 📝 설명

### Kinesis Data Analytics + Lambda가 복잡한 데이터 전처리에 적합한 이유

라이드쉐어링과 같은 실시간 비즈니스 데이터는 단순한 포맷 변환을 넘어서는 복잡한 전처리가 필요하다. Kinesis Data Analytics는 SQL을 통해 실시간 집계, 윈도우 함수, 조인 등을 수행하고, Lambda는 비즈니스 로직, 데이터 검증, 외부 시스템 연동 등 프로그래밍이 필요한 작업을 담당한다.

### 아키텍처 플로우

```
스트리밍 데이터 → Kinesis Data Streams 
                      ↓
               Kinesis Data Analytics (SQL 처리)
                 - 실시간 집계
                 - 윈도우 함수
                 - 데이터 정리
                      ↓
               Lambda Function (커스텀 처리)
                 - 비즈니스 로직
                 - 데이터 검증
                 - 변환 및 정규화
                      ↓
                 S3 Data Lake → SageMaker
```

### Trade-offs 고려사항

**Kinesis Data Analytics + Lambda 장점**:
- 복잡한 SQL 쿼리와 커스텀 로직 조합 가능
- 실시간 집계 및 윈도우 함수 지원
- 외부 시스템 연동 및 API 호출 가능
- 데이터 품질 검증 및 복잡한 변환 로직 처리

**Kinesis Data Analytics + Lambda 단점**:
- 여러 서비스 조합으로 아키텍처 복잡성 증가
- Lambda 동시성 제한 및 15분 실행 시간 제약
- 비용이 상대적으로 높음

**Kinesis Data Firehose 장점**:
- 단일 서비스로 간단한 아키텍처
- 완전 관리형으로 운영 부담 없음
- 자동 스케일링 및 배치 처리 최적화
- 비용 효율적

**Kinesis Data Firehose 단점**:
- 단순한 변환 기능만 제공 (포맷 변환, 압축, 파티셔닝)
- 복잡한 비즈니스 로직 처리 불가
- 데이터 품질 검증, 조건부 처리 등 불가능

## 🔍 주요개념

### 서비스별 처리 범위 비교

- **Kinesis Data Analytics**: SQL 기반 실시간 스트림 처리, 집계, 윈도우 함수, 조인 등 복잡한 쿼리 가능
- **Kinesis Data Firehose**: 데이터 포맷 변환, 압축, 파티셔닝 등 기본적인 변환만 지원
- **Lambda**: 프로그래밍 기반 커스텀 로직, API 연동, 복잡한 변환 처리

### 데이터 처리 복잡도별 서비스 선택

```
복잡성 ↑
│  [EMR/Glue]        [Analytics+Lambda]
│  대용량+복잡        중용량+복잡
│  
│  [Firehose]        [Lambda만]
│  대용량+단순        소용량+복잡
└─────────────────── 용량 →
```

### 실전 적용

- **라이드쉐어링 데이터**: 실시간 집계 + 복잡한 비즈니스 로직 → Analytics + Lambda
- **로그 파일 저장**: 단순 포맷 변환 + 압축 → Firehose
- **IoT 센서 데이터**: 실시간 이상 탐지 + 집계 → Analytics + Lambda

## 📝 관련 문제

**Question:** You work as a machine learning specialist at a ride sharing software company. You need to analyze the streaming ride data of your firm's drivers. First, you need to clean, organize, and transform the drive data and load it into your firm's data lake so you can then use the data in your machine learning models in SageMaker. Which AWS services would give you the simplest solution?

**Options:**

- A) Use Amazon Kinesis Data Streams to capture the streaming ride data. Use Amazon Kinesis Data Analytics to clean, organize, and transform the drive data and then output the data to your S3 data lake using a Lambda function.
- B) Use Amazon Kinesis Data Streams to capture the streaming ride data. Have Amazon Kinesis Data Streams trigger a lambda function to clean, organize, and transform the drive data and then output the data to your S3 data lake.
- C) Use Amazon Kinesis Data Streams to capture the streaming ride data. Have Kinesis Data Streams stream the data to a set of processing workers running in ECS Fargate. The workers send the data to your S3 data lake.
- D) Use Amazon Kinesis Data Firehose to stream the data directly to your S3 data lake.

**정답: A) Amazon Kinesis Data Analytics + Lambda**

💡 추가 설명:

- **옵션 B (Data Streams + Lambda만)** - Lambda 혼자서는 실시간 스트리밍 집계와 윈도우 함수 처리가 어렵고 동시성 제한 존재
- **옵션 C (Data Streams + ECS Fargate)** - 확장성은 좋지만 인프라 관리 복잡성 증가로 "simplest solution" 조건에 부적합  
- **옵션 D (Firehose만)** - 단순한 포맷 변환만 가능하고 복잡한 clean, organize 작업 불가능