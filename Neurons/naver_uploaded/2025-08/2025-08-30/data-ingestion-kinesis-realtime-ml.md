---

title: data-ingestion-kinesis-realtime-ml
created: 2025-08-20
modified: 2025-08-20
tags:
- service/kinesis
- problem/data-ingestion
- constraint/real-time
- usecase/machine-learning
- technique/streaming
aliases: ["kinesis-streams", "ml-ingestion", "streaming-data"]

---

# AWS Kinesis Data Streams를 활용한 실시간 ML 데이터 수집

## 🎯 핵심 포인트

IoT 센서, 소셜미디어, 데이터베이스 등 다양한 소스에서 실시간으로 데이터를 수집해야 하는 경우 Amazon Kinesis Data Streams에서, 확장 가능한 스트리밍 데이터 파이프라인을 구축할 수 있다.

## 📝 설명

### Amazon Kinesis Data Streams가 ML 데이터 수집에 적합한 이유

Amazon Kinesis Data Streams는 실시간 스트리밍 데이터를 처리하도록 설계된 완전관리형 서비스로, 초당 수백만 개의 레코드를 처리할 수 있는 확장성을 제공합니다. 각 스트림은 여러 개의 샤드로 구성되며, 각 샤드는 초당 1,000개의 레코드 또는 1MB의 데이터를 처리할 수 있습니다. 데이터는 1일부터 365일까지 구성 가능한 기간 동안 보존되어 재처리가 가능합니다.

**다중 소스 데이터 수집 방식:**

1. **단일 스트림 접근법**: 모든 소스가 하나의 스트림으로 데이터를 전송하고, 레코드에 `source_type` 필드로 구분
2. **다중 스트림 접근법**: 각 소스별로 개별 스트림을 생성하고, Kinesis Data Analytics에서 통합 처리

**여러 스트림을 하나의 Analytics로 통합:**
- 여러 Kinesis 스트림을 단일 Kinesis Data Analytics 애플리케이션에 연결 가능
- SQL을 통한 실시간 크로스 스트림 조인 및 집계 처리
- 각 스트림의 특성에 맞는 개별 전처리 후 통합 분석

### 아키텍처 플로우

**단일 스트림 방식:**
```
IoT Sensors ────┐
Social Media ───┼──→ Single Kinesis Stream ─→ Analytics ─→ S3/DynamoDB ─→ SageMaker
Database CDC ───┘
```

**다중 스트림 방식:**
```
IoT Sensors ──→ iot-stream ────┐
                               ├──→ Kinesis Analytics App ─→ ML Pipeline
Social Media ─→ social-stream ──┤
                               │
Database ─────→ db-stream ──────┘
```

### Trade-offs 고려사항

**Amazon Kinesis Data Streams 장점**:

- 실시간 데이터 처리로 밀리초 단위 지연 시간
- 자동 확장 및 고가용성 제공
- 여러 컨슈머가 동일한 데이터를 동시에 처리 가능
- AWS ML 서비스와의 네이티브 통합

**Amazon Kinesis Data Streams 단점**:

- 샤드 관리 및 비용 최적화 필요
- 복잡한 데이터 변환 시 추가 서비스 필요
- 처리량에 따른 비용 증가

**Amazon S3 장점**:

- 대용량 데이터 저장에 적합
- 저렴한 스토리지 비용

**Amazon S3 단점**:

- 실시간 데이터 수집 기능 없음
- 배치 처리에만 적합

**Amazon SNS 장점**:

- 간단한 알림 및 메시징 서비스

**Amazon SNS 단점**:

- 데이터 수집 및 저장 기능 없음
- 일회성 메시지 전달에만 적합

## 🔍 주요개념

### 스트리밍 vs 배치 처리 비교

- **스트리밍 처리**: 데이터가 생성되는 즉시 처리하여 실시간 인사이트 제공
- **배치 처리**: 일정 시간 간격으로 데이터를 모아서 처리하는 방식

### 실전 적용

**단일 스트림 사용 사례:**
- 실시간 사기 탐지: 신용카드 거래, 로그인 이벤트, 사용자 행동 데이터를 하나의 스트림으로 수집하여 통합 분석
- 종합 대시보드: 모든 소스 데이터를 시간순으로 정렬하여 실시간 모니터링

**다중 스트림 사용 사례:**
- IoT 디바이스 모니터링: 센서별로 개별 스트림을 생성하고, Analytics에서 교차 분석으로 예측 정비
- 소셜미디어 감정 분석: 플랫폼별 스트림을 생성하고, 통합 Analytics에서 브랜드 평판 종합 분석

**크로스 스트림 Analytics 적용:**
- 다중 소스 상관관계 분석: IoT 센서 데이터와 소셜미디어 반응, 데이터베이스 트랜잭션을 실시간으로 조인하여 비즈니스 인사이트 도출

## 📝 관련 문제

**Question:** A company wants to build a machine learning solution that can process data from various sources including IoT sensors, social media platforms, and databases. Which AWS service should be used to ingest and collect this real-time streaming data?

**Options:**

- A) Amazon Simple Storage Service (S3)
- B) Amazon Simple Notification Service (SNS)
- C) Amazon Elastic File System (EFS)
- D) Amazon Kinesis Data Streams
- E) AWS Direct Connect

**정답: D) Amazon Kinesis Data Streams**

💡 추가 설명:

- **Amazon S3** - 객체 스토리지 서비스로 데이터 저장에는 적합하지만 실시간 데이터 수집 기능은 제공하지 않음
- **Amazon SNS** - 푸시 알림 서비스로 메시지 전달에만 사용되며 데이터 수집 및 저장 기능 없음
- **Amazon EFS** - 파일 스토리지 서비스로 공유 파일 저장에 사용되며 스트리밍 데이터 수집에 부적합
- **AWS Direct Connect** - 온프레미스와 AWS 간의 전용 네트워크 연결 서비스로 데이터 수집 기능 없음