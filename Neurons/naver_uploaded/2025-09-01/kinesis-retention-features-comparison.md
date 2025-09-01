---

title: kinesis-retention-features-comparison
created: 2025-08-21
modified: 2025-08-21
tags:
- service/kinesis
- feature/retention-period
- constraint/data-persistence
- concept/streaming-limits
- exam/aws-certification
aliases: ["kinesis-features", "kds-retention", "streaming-data-limits"]

---

# Amazon Kinesis Data Streams 기능과 제약사항 분석

## 🎯 핵심 포인트

실시간 스트리밍 데이터 처리에서 Kinesis Data Streams의 올바른 기능과 제약사항을 이해한 경우, 잘못된 기능 설명을 구분하여 적절한 아키텍처 설계를 할 수 있다.

## 📝 설명

### Kinesis Data Streams가 실시간 스트리밍에 적합한 이유

Amazon Kinesis Data Streams는 실시간 스트리밍 데이터를 대규모로 수집, 저장, 처리할 수 있는 완전 관리형 서비스입니다. 하지만 특정 기능에 대한 정확한 이해가 필요하며, 특히 데이터 보존 기간과 데이터 소스 연결 방식에서 자주 오해가 발생합니다.

### 아키텍처 플로우

```
Data Sources → [API/SDK] → Kinesis Data Streams → [Consumers] → Processing Applications
    ↓              ↓              ↓                 ↓              ↓
IoT Devices    PutRecords     Shards(24h~365d)   GetRecords    Analytics/Storage
Log Files      Producer Lib   Auto-scaling      Enhanced       Real-time Actions
Applications   Custom Code    Multi-AZ Rep.     Fan-out        Dashboards
```

### Trade-offs 고려사항

**Kinesis Data Streams 장점**:
- 실시간 처리 지원 (1초 미만 지연)
- 자동 스케일링과 높은 처리량
- 다중 소비자 지원 (Enhanced Fan-out)
- 최대 365일까지 데이터 보존 가능

**Kinesis Data Streams 단점**:
- 직접적인 데이터 소스 연결 기능 없음 (API 호출 필요)
- 24시간 이후 보존 시 추가 비용 발생
- 샤드 관리 복잡성 (Provisioned 모드)

**Amazon Kinesis Firehose 장점**:
- 데이터 소스에서 목적지로 직접 전송
- 서버리스 운영 (샤드 관리 불필요)
- 다양한 목적지 지원 (S3, Redshift 등)

**Amazon Kinesis Firehose 단점**:
- 실시간 처리 제한 (최소 60초 지연)
- 데이터 보존 없음 (일회성 전송)
- 다중 소비자 지원 제한

## 🔍 주요개념

### 핵심 기능 비교

- **데이터 보존 기간**: 기본 24시간, 최대 365일까지 확장 가능 (7일이 아님)
- **데이터 수집 방식**: PutRecords API나 Producer Library를 통한 능동적 전송 (자동 수집 아님)
- **자동 스케일링**: 데이터 볼륨에 따른 처리 용량 자동 조정
- **실시간 분석**: Kinesis Analytics를 통한 SQL 기반 스트림 분석

### 실전 적용

- IoT 센서 데이터 수집 시 SDK를 통한 PutRecords 호출로 데이터 전송
- 애플리케이션 로그 분석을 위한 365일 장기 보존 설정
- 전자상거래 실시간 추천을 위한 Enhanced Fan-out 활용

## 📝 관련 문제

**Question:** Which of the following is NOT a valid feature of Amazon Kinesis Data Streams?

**Options:**

- A) Ability to store, process, and analyze real-time streaming data at scale
- B) Automatic scaling of streaming capacity to accommodate changes in data volume  
- C) Retention period of up to 7 days for streamed data
- D) Ability to perform real-time analytics on streaming data using Kinesis Analytics
- E) Ability to write data to Kinesis Data Streams from any data source, such as IoT devices or log files

**정답: E) Ability to write data to Kinesis Data Streams from any data source**

💡 추가 설명:

- **Option A** - ✅ 올바른 기능: 대규모 실시간 스트리밍 데이터 처리는 Kinesis의 핵심 기능
- **Option B** - ✅ 올바른 기능: 데이터 볼륨에 따른 자동 스케일링 지원
- **Option C** - ❌ 부분적으로 틀림: 최대 365일까지 보존 가능하므로 "7일까지"라는 제한이 부정확
- **Option D** - ✅ 올바른 기능: Kinesis Analytics를 통한 실시간 SQL 분석 지원
- **Option E** - ❌ 완전히 틀림: 모든 데이터 소스에서 자동으로 데이터를 수집하는 내장 기능은 없음. 반드시 PutRecords API 호출이나 Producer Library 사용 필요

---