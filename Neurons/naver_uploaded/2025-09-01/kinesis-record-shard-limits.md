---

title: kinesis-record-shard-limits
created: 2025-08-21
modified: 2025-08-21
tags:
- service/kinesis-data-streams
- constraint/throughput-limits
- data/record-size
- performance/shard-capacity
- streaming/real-time
aliases: ["kinesis-limits", "shard-capacity", "record-size-limit"]

---

# Amazon Kinesis Data Streams 레코드 및 샤드 용량 제한

## 🎯 핵심 포인트

실시간 데이터 스트리밍이 필요한 경우 Amazon Kinesis Data Streams에서, 단일 레코드는 최대 512KB이며 샤드당 초당 1,000개 레코드 또는 1MB 처리량 제한을 고려하여 설계할 수 있다.

## 📝 설명

### Amazon Kinesis Data Streams가 실시간 데이터 스트리밍에 적합한 이유

Amazon Kinesis Data Streams는 확장 가능하고 내구성 있는 실시간 데이터 스트리밍 서비스로, 시간당 테라바이트 규모의 데이터를 처리할 수 있습니다. 샤드 기반 아키텍처를 통해 수평적 확장이 가능하며, 데이터 파티셔닝과 순서 보장을 제공합니다.

### 용량 제한 구조

**레코드 수준 제한**:
- 단일 레코드 최대 크기: **512KB**
- 더 큰 데이터는 여러 레코드로 분할 필요

**샤드 수준 처리량**:
- **쓰기**: 초당 1,000개 레코드 **또는** 초당 1MB (둘 중 먼저 도달하는 제한 적용)
- **읽기**: 초당 5회 트랜잭션 **또는** 초당 2MB

### 아키텍처 플로우

```
Producer → [Partition Key] → Shard 1 (1MB/sec, 1000 records/sec)
                         → Shard 2 (1MB/sec, 1000 records/sec)
                         → Shard N (1MB/sec, 1000 records/sec)
                                     ↓
Consumer 1 (2MB/sec, 5 reads/sec) ← Stream
Consumer 2 (2MB/sec, 5 reads/sec) ← Stream
```

### Trade-offs 고려사항

**Kinesis Data Streams 장점**:
- 실시간 처리 가능 (밀리초 지연)
- 순서 보장 (샤드 내)
- 자동 확장/축소 (On-Demand 모드)
- 최대 365일 데이터 보존

**Kinesis Data Streams 단점**:
- 샤드 관리 복잡성
- 처리량 제한 초과 시 에러 발생
- 상대적으로 높은 비용

**Amazon SQS 장점**:
- 메시지 크기 256KB 제한이지만 관리 단순
- 무제한 처리량 (표준 큐)

**Amazon SQS 단점**:
- 순서 보장 제한적 (FIFO 큐 필요)
- 실시간 스트리밍보다는 비동기 메시징에 적합

## 🔍 주요개념

### 처리량 계산 예시

- **512KB 레코드**: 샤드당 초당 약 2개 (1MB ÷ 512KB)
- **100KB 레코드**: 샤드당 초당 약 10개 (1MB ÷ 100KB)  
- **1KB 레코드**: 샤드당 초당 1,000개 (레코드 수 제한)

### 실전 적용

- **IoT 센서 데이터**: 작은 레코드, 높은 빈도
- **로그 집계**: 중간 크기 레코드, 지속적 스트림
- **실시간 분석**: 대용량 레코드, 낮은 빈도

## 📝 관련 문제

**Question:** A company needs to stream real-time clickstream data from their e-commerce platform. Each click event is approximately 2KB, and they expect 10,000 events per second during peak hours. What is the minimum number of Kinesis Data Streams shards needed?

**Options:**

- A) 1 shard
- B) 5 shards  
- C) 10 shards
- D) 20 shards
- E) 50 shards

**정답: D) 20 shards**

💡 추가 설명:

- **Option A (1 shard)** - 샤드당 초당 1,000개 레코드 제한으로 10,000 이벤트 처리 불가
- **Option B (5 shards)** - 5,000개 레코드만 처리 가능 (5 × 1,000)
- **Option C (10 shards)** - 10,000개 레코드 처리 가능하지만 데이터 크기도 고려 필요 (10,000 × 2KB = 20MB > 10MB)
- **Option E (50 shards)** - 과도한 프로비저닝으로 비용 비효율