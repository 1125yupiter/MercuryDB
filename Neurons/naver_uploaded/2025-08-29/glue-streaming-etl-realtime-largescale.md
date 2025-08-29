---

title: glue-streaming-etl-realtime-largescale
created: 2025-08-20
modified: 2025-08-20
tags:
- service/glue
- constraint/near-real-time
- processing/large-scale
- technique/streaming-etl
- architecture/distributed
aliases: ["glue-streaming", "real-time-etl", "streaming-transform"]

---

# AWS Glue Streaming을 활용한 대규모 실시간 ETL 처리

## 🎯 핵심 포인트

대규모 데이터셋을 거의 실시간으로 ETL 처리해야 하는 경우, AWS Glue Streaming ETL Jobs와 Kinesis를 조합하여 페타바이트급 데이터의 분산 스트림 처리를 수행할 수 있다.

## 📝 설명

### AWS Glue Streaming이 대규모 실시간 ETL에 적합한 이유

AWS Glue Streaming ETL Jobs는 Apache Spark Structured Streaming을 기반으로 하여 마이크로배치 방식의 거의 실시간 처리를 제공합니다. 초 단위로 데이터를 처리하면서도 Spark의 분산 처리 능력을 활용하여 대용량 데이터를 효율적으로 변환할 수 있습니다.

완전한 서버리스 환경에서 자동 스케일링이 지원되어, 데이터 볼륨의 변화에 따라 워커 노드가 자동으로 증감됩니다. 이를 통해 페타바이트급 데이터도 안정적으로 처리할 수 있으며, 복잡한 데이터 변환 로직도 Python이나 Scala로 구현 가능합니다.

### 아키텍처 플로우

```
데이터 소스 → Kinesis Data Streams → AWS Glue Streaming ETL Jobs → 변환 처리
     ↓                                        ↓                      ↓
실시간 수집     마이크로배치 처리              분산 변환 로직           목적지 저장
     ↓                                        ↓                      ↓
애플리케이션      초 단위 처리 간격           Spark 분산 처리      S3/Redshift/RDS
로그/이벤트      자동 체크포인팅              자동 스케일링         실시간 분석 준비
```

### Trade-offs 고려사항

**AWS Glue Streaming 장점**:
- Apache Spark 기반의 강력한 분산 처리 능력
- 완전한 ETL 기능 (Extract, Transform, Load) 지원
- 서버리스 환경에서 자동 스케일링
- 복잡한 변환 로직 구현 가능
- 다양한 데이터 소스 및 대상 지원

**AWS Glue Streaming 단점**:
- 진정한 실시간이 아닌 마이크로배치 방식 (초~분 단위 지연)
- 콜드 스타트로 인한 초기 지연 시간
- Kinesis Data Streams 대비 상대적으로 높은 비용

**Amazon Kinesis Firehose 장점**:
- 진정한 실시간 스트리밍 처리
- 간단한 설정과 관리
- 낮은 지연시간 (초 단위)
- 비용 효율적

**Amazon Kinesis Firehose 단점**:
- 제한적인 변환 기능 (Lambda 함수나 외부 서비스 의존)
- 복잡한 ETL 로직 구현 어려움
- 조인, 집계 등 고급 변환 기능 부족

## 🔍 주요개념

### 실시간 vs 거의 실시간 처리

- **실시간 (Real-time)**: 밀리초~초 단위 지연, 스트림 기반 처리
- **거의 실시간 (Near Real-time)**: 초~분 단위 지연, 마이크로배치 처리
- **배치 처리**: 시간~일 단위 지연, 대용량 일괄 처리

### 실전 적용

- **실시간 사기 탐지**: 금융 거래 데이터를 스트림 처리하여 이상 패턴 감지
- **IoT 센서 데이터 분석**: 수천 개 센서에서 발생하는 대용량 시계열 데이터 변환
- **실시간 추천 시스템**: 사용자 행동 데이터를 실시간으로 가공하여 개인화 추천 모델 업데이트

## 📝 관련 문제

**Question:** A company needs to process terabytes of streaming data from IoT devices in near real-time, applying complex transformations including joins and aggregations before loading into a data warehouse. Which AWS service combination would be most suitable?

**Options:**

- A) Amazon Kinesis Firehose with Lambda functions
- B) AWS Glue Streaming ETL Jobs with Kinesis Data Streams
- C) AWS Step Functions with AWS Batch
- D) Amazon S3 with scheduled Lambda functions
- E) AWS Data Pipeline with EMR clusters

**정답: B) AWS Glue Streaming ETL Jobs with Kinesis Data Streams**

💡 추가 설명:

- **Kinesis Firehose + Lambda** - 복잡한 조인과 집계 작업에는 Lambda의 15분 실행 시간 제한과 메모리 제약으로 부적합
- **Step Functions + Batch** - 배치 처리 방식으로 실시간 요구사항을 충족할 수 없음
- **S3 + Lambda** - 예약 기반 처리로 실시간성 부족, 대용량 스트리밍 데이터 처리에 비효율적
- **Data Pipeline + EMR** - 전통적인 배치 ETL 방식으로 실시간 스트림 처리에 적합하지 않음