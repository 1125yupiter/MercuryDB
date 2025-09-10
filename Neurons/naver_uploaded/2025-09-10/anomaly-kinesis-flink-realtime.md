---

title: anomaly-kinesis-flink-realtime
created: 2025-08-27
modified: 2025-08-27
tags:
- problem/anomaly-detection
- service/kinesis-analytics
- constraint/real-time
- technique/window-processing
- usecase/podcast-engagement

aliases: ["kinesis-flink", "stream-anomaly", "flink-windows"]

---

# 실시간 이상 탐지를 위한 Kinesis Data Analytics for Apache Flink 활용

## 🎯 핵심 포인트

스트리밍 데이터의 실시간 이상 탐지가 필요한 경우 Kinesis Data Analytics for Apache Flink에서, 10분 슬라이딩 윈도우를 활용한 완전 관리형 스트림 처리를 구현할 수 있다.

## 📝 설명

### Apache Flink가 실시간 이상 탐지에 적합한 이유

Apache Flink는 실시간 스트림 처리에 특화된 오픈소스 프레임워크로, AWS에서 완전 관리형 서비스로 제공됩니다. 특히 **윈도우 처리의 전문가**로서 복잡한 시간 기반 집계와 이벤트 패턴 분석에 탁월한 성능을 보입니다.

**핵심 특징:**
- 밀리초 단위 저지연 처리
- 정확히 한 번(exactly-once) 처리 보장
- 복잡한 이벤트 처리(CEP) 지원
- Event Time 기반 정확한 윈도우 처리
- 자동 장애 복구 및 상태 관리

**윈도우 처리 능력:**
```java
// 10분 슬라이딩 윈도우 예시
podcastEvents
  .keyBy(event -> event.getUserId())
  .timeWindow(Time.minutes(10), Time.minutes(1))
  .aggregate(new EngagementAggregator())
  .addSink(new AnomalyDetectionSink());
```

### 아키텍처 플로우

```
Podcast User Events → Kinesis Data Streams → Kinesis Analytics (Apache Flink)
                                                    ↓
                                          [10분 윈도우 집계 처리]
                                                    ↓
                                          [이벤트 페이로드 변환]
                                                    ↓
                                          [이상 탐지 알고리즘 실행]
                                                    ↓
                                            실시간 알림/액션
```

### Trade-offs 고려사항

**Kinesis Analytics for Apache Flink 장점:**
- 완전 서버리스 관리형 서비스 (인프라 관리 불필요)
- 강력한 윈도우 처리 기능 (시간/개수/세션 기반)
- 실시간 처리 (밀리초 지연시간)
- 자동 스케일링 및 장애 복구
- SQL과 Java/Scala 모두 지원

**Kinesis Analytics for Apache Flink 단점:**
- 상대적으로 높은 비용 (지속적 실행)
- 복잡한 설정과 디버깅
- Flink 전문 지식 필요

**Lambda 장점:**
- 서버리스로 사용한 만큼만 과금
- 간단한 이벤트 처리에 적합
- 빠른 개발 및 배포

**Lambda 단점:**
- 15분 실행 제한으로 지속적 스트림 처리 불가능
- 복잡한 윈도우 처리 로직 구현 어려움
- 상태 관리의 한계

**AWS Glue 단점:**
- 배치 처리 도구로 실시간 처리 불가
- 분/시간 단위 지연으로 실시간 이상 탐지 부적합

## 🔍 주요개념

### 윈도우 처리 타입 비교

- **Tumbling Window**: 겹치지 않는 고정 크기 윈도우 (예: 매 10분마다)
- **Sliding Window**: 겹치는 윈도우 (예: 1분마다 이동하는 10분 윈도우)
- **Session Window**: 비활성 기간으로 구분되는 동적 윈도우

### 실전 적용

- **Podcast 플랫폼**: 사용자별 10분간 engagement 패턴 분석으로 이탈 예측
- **IoT 센서 모니터링**: 실시간 센서 데이터 이상치 탐지 및 알림
- **금융 거래**: 실시간 사기 거래 패턴 탐지 및 차단
- **게임 서비스**: 플레이어 행동 패턴 분석으로 치팅 탐지
- **광고 플랫폼**: 실시간 클릭/노출 패턴 분석으로 이상 트래픽 탐지

## 📝 관련 문제

**Question:** A company has a podcast platform that has thousands of users. The company has implemented an anomaly detection algorithm to detect low podcast engagement based on a 10-minute running window of user events such as listening, pausing, and exiting the podcast. A machine learning (ML) specialist is designing the data ingestion of these events with the knowledge that the event payload needs some small transformations before inference. How should the ML specialist design the data ingestion to meet these requirements with the LEAST operational overhead?

**Options:**

- A) Ingest event data by using a GraphQL API in AWS AppSync. Store the data in an Amazon DynamoDB table. Use DynamoDB Streams to call an AWS Lambda function to transform the most recent 10 minutes of data before inference.
- B) Ingest event data by using Amazon Kinesis Data Streams. Store the data in Amazon S3 by using Amazon Kinesis Data Firehose. Use AWS Glue to transform the most recent 10 minutes of data before inference.
- C) Ingest event data by using Amazon Kinesis Data Streams. Use an Amazon Kinesis Data Analytics for Apache Flink application to transform the most recent 10 minutes of data before inference.
- D) Ingest event data by using Amazon Managed Streaming for Apache Kafka (Amazon MSK). Use an AWS Lambda function to transform the most recent 10 minutes of data before inference.

**정답: C) Amazon Kinesis Data Streams + Kinesis Data Analytics for Apache Flink**

💡 추가 설명:

- **옵션 A (AppSync + DynamoDB)** - GraphQL API는 대용량 스트리밍에 최적화되지 않으며, DynamoDB는 실시간 스트림 처리보다는 NoSQL 데이터베이스로 적합
- **옵션 B (Firehose + Glue)** - 배치 처리 구조로 실시간성이 부족하고, Glue는 ETL 배치 도구로 실시간 스트림 처리에 부적절
- **옵션 D (MSK + Lambda)** - MSK는 Kafka 클러스터 운영이 필요해 운영 오버헤드가 높고, Lambda는 15분 실행 제한으로 지속적 스트림 처리에 부적절