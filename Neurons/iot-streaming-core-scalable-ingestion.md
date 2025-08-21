---

title: iot-streaming-core-scalable-ingestion
created: 2025-08-21
modified: 2025-08-21
tags:
- service/iot-core
- constraint/real-time
- constraint/scalable
- usecase/streaming-data
- deployment/device-fleet
aliases: ["IoT Core", "AWS IoT", "streaming ingestion"]

---

# AWS IoT Core를 활용한 IoT 디바이스 플릿 스트리밍 데이터 수집

## 🎯 핵심 포인트

IoT 디바이스 플릿에서 스트리밍 데이터를 안전하고 확장 가능하며 저지연으로 수집하고 저장해야 하는 경우 AWS IoT Core에서, 디바이스 연결 관리와 데이터 라우팅을 통합적으로 처리할 수 있다.

## 📝 설명

### AWS IoT Core가 IoT 스트리밍 데이터 수집에 적합한 이유

AWS IoT Core는 IoT 환경에 특화된 완전 관리형 클라우드 서비스로, 디바이스 연결부터 데이터 처리까지 통합된 솔루션을 제공합니다. 2025년 현재도 활발히 업데이트되며, 수백만 개의 디바이스를 동시에 지원하는 확장성을 갖추고 있습니다.

**핵심 기능**:
- **Device Gateway**: MQTT, HTTP, WebSocket 프로토콜을 통한 안전한 양방향 통신
- **Rules Engine**: 실시간 데이터 라우팅 및 필터링
- **Device Shadow**: 오프라인 디바이스 상태 관리
- **Device Registry**: 중앙 집중식 디바이스 관리

### 아키텍처 플로우

```
IoT Devices → Device Gateway → Message Broker → Rules Engine → Target Services
    ↓              ↓               ↓              ↓              ↓
[센서/액추에이터] [인증/암호화]   [MQTT 처리]    [데이터 라우팅]  [S3/DynamoDB/Kinesis]
    ↓              ↓               ↓              ↓              ↓
[실시간 데이터]   [보안 연결]     [메시지 큐잉]   [조건부 처리]   [영구 저장/분석]
```

### Trade-offs 고려사항

**AWS IoT Core 장점**:
- IoT 전용 보안 기능 (디바이스별 인증서, TLS 암호화)
- 자동 확장 (인프라 관리 불필요)
- 저지연 실시간 통신
- AWS 생태계와의 완벽한 통합
- 디바이스 라이프사이클 관리

**AWS IoT Core 단점**:
- 메시지 처리량 기반 과금 (대용량 시 비용 증가)
- AWS 벤더 종속성
- 복잡한 설정 (초기 학습 곡선)

**Amazon S3 장점**:
- 대용량 데이터 저장에 경제적
- 높은 내구성과 가용성

**Amazon S3 단점**:
- 스트리밍 데이터 수집에 최적화되지 않음
- 실시간 처리 불가
- IoT 디바이스 관리 기능 부재

**Amazon Kinesis 장점**:
- 스트리밍 데이터 처리에 특화
- 높은 처리량 지원

**Amazon Kinesis 단점**:
- IoT 디바이스 보안 및 관리 기능 부족
- 디바이스 연결 관리 불가

## 🔍 주요개념

### 핵심 개념 비교

- **AWS IoT Core**: IoT 전용 플랫폼으로 디바이스 관리부터 데이터 처리까지 통합 제공
- **AWS IoT Greengrass**: 엣지 컴퓨팅 플랫폼으로 로컬에서 AWS 서비스 실행 (IoT Core의 확장)

### 실전 적용

- 스마트 팩토리의 센서 데이터 실시간 모니터링 및 예측 정비
- 커넥티드 카의 텔레매틱스 데이터 수집 및 분석
- 스마트 홈 기기들의 상태 관리 및 자동화 제어

## 📝 관련 문제

**Question:** You need to ingest and store streaming data from a fleet of IoT devices. Which AWS service would you use to ensure secure, scalable, and low-latency data ingestion and storage?

**Options:**

- A) Amazon S3
- B) AWS IoT Core  
- C) Amazon Elasticsearch Service
- D) AWS Glue
- E) Amazon Kinesis Data Streams

**정답: B) AWS IoT Core**

💡 추가 설명:

- **Amazon S3** - 대용량 저장소이지만 스트리밍 데이터 수집이나 실시간 처리에 최적화되지 않으며 IoT 디바이스 관리 기능 부재
- **Amazon Elasticsearch Service** - 검색 및 분석 엔진으로 IoT 데이터 수집용이 아니며 디바이스 연결 관리 불가
- **AWS Glue** - ETL 서비스로 실시간 IoT 데이터 수집에 부적합하며 배치 처리 위주
- **Amazon Kinesis Data Streams** - 스트리밍 처리에 특화되었지만 IoT 디바이스 보안, 인증, 관리 기능이 없어 IoT 환경에 부적합

---