---
title: recommendation-kinesis-realtime-multishard
created: 2025-08-19
modified: 2025-08-19
tags:
- service/kinesis-data-analytics
- usecase/recommendation-engine
- constraint/real-time
- constraint/high-volume
- architecture/multi-shard
aliases: ["kinesis-recommendation", "streaming-analytics", "real-time-rec"]

---

# 실시간 추천 엔진을 위한 Kinesis Data Analytics 다중 샤드 구성

## 🎯 핵심 포인트

고볼륨 스트리밍 데이터를 처리하는 실시간 추천 엔진을 구축할 경우, 입력과 출력 스트림 모두에 다중 샤드를 사용하면 확장성, 내결함성, 낮은 지연시간을 보장할 수 있다.

## 📝 설명

### Kinesis Data Analytics가 실시간 추천 엔진에 적합한 이유

Amazon Kinesis Data Analytics는 관리형 스트리밍 데이터 처리 서비스로, 실시간 추천 엔진의 핵심 요구사항인 높은 처리량, 낮은 지연시간, 내결함성을 만족합니다. 

**다중 샤드 구성의 핵심 특징:**
- **병렬 처리**: 여러 샤드에 데이터를 분산하여 동시 처리
- **자동 확장**: 트래픽 증가 시 샤드 수를 동적으로 조정
- **장애 격리**: 개별 샤드 장애 시에도 전체 시스템 가용성 유지
- **부하 분산**: 데이터 처리 부하를 여러 처리 단위에 균등 분배

### 아키텍처 플로우

```
사용자 행동 데이터
        ↓
[입력 다중 샤드]
   Shard 1 → 처리 단위 1
   Shard 2 → 처리 단위 2  
   Shard 3 → 처리 단위 3
   Shard N → 처리 단위 N
        ↓
[Kinesis Data Analytics]
- 실시간 집계 및 분석
- ML 모델 추론
- 추천 알고리즘 실행
        ↓
[출력 다중 샤드]
   Shard 1 → 추천 서비스 1
   Shard 2 → 추천 서비스 2
   Shard 3 → 추천 서비스 3
   Shard N → 추천 서비스 N
        ↓
실시간 개인화 추천
```

### Trade-offs 고려사항

**다중 샤드 구성 장점**:
- 높은 처리량 (각 샤드당 1MB/s 또는 1,000 레코드/s)
- 수평적 확장성 (샤드 추가로 성능 향상)
- 장애 격리 및 내결함성
- 병렬 처리로 인한 낮은 전체 지연시간

**다중 샤드 구성 단점**:
- 비용 증가 (샤드 수에 비례한 요금)
- 데이터 순서 보장의 복잡성
- 샤드 간 데이터 분배 로직 필요
- 모니터링 및 관리 복잡성 증가

**단일 샤드 구성 장점**:
- 단순한 아키텍처
- 데이터 순서 보장
- 낮은 비용

**단일 샤드 구성 단점**:
- 처리량 한계 (1MB/s)
- 확장성 부족
- 단일 실패점
- 높은 지연시간 (병목 현상)

## 🔍 주요개념

### 샤드 구성 비교

- **입력 다중/출력 단일**: 입력 처리는 빠르지만 출력 병목으로 전체 성능 저하
- **입력 단일/출력 다중**: 입력 처리 한계로 다중 출력의 이점 활용 불가
- **입력 다중/출력 다중**: 전체 파이프라인의 병목 제거로 최적 성능

### 실전 적용

- **전자상거래 실시간 상품 추천**: 사용자 클릭, 구매 데이터 스트리밍 처리
- **동영상 스트리밍 개인화**: 시청 패턴 분석 및 실시간 콘텐츠 추천
- **금융 서비스 개인화**: 거래 패턴 기반 맞춤형 금융 상품 추천

## 📝 관련 문제

**Question:** You are building a real-time recommendation engine that requires processing a high volume of streaming data. You plan to use Amazon Kinesis Data Analytics to process the data. You need to ensure that your application is scalable, fault-tolerant, and provides low-latency processing. Which configuration would you recommend?

**Options:**

- A) Use Amazon Kinesis Data Streams instead of Kinesis Data Analytics
- B) Use a single shard for the input stream and a single shard for the output stream
- C) Use multiple shards for the input stream and a single shard for the output stream
- D) Use multiple shards for the input stream and multiple shards for the output stream
- E) Use a single shard for the input stream and multiple shards for the output stream

**정답: D) Use multiple shards for the input stream and multiple shards for the output stream**

💡 추가 설명:

- **A) Kinesis Data Streams 사용** - 추가 처리 단계가 필요하여 지연시간 증가 및 복잡성 상승
- **B) 단일 입력/단일 출력** - 처리량 한계와 단일 실패점으로 고볼륨 처리 부적합
- **C) 다중 입력/단일 출력** - 출력 병목으로 인한 전체 시스템 성능 저하
- **E) 단일 입력/다중 출력** - 입력 처리 한계로 확장성 제약