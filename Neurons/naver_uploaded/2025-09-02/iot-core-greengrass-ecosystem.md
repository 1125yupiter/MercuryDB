---

title: iot-core-greengrass-ecosystem
created: 2025-08-22
modified: 2025-08-22
tags:
- service/iot-core
- service/iot-greengrass
- protocol/mqtt
- architecture/edge-cloud
- concept/iot-ecosystem
aliases: ["iot-services", "iot-architecture", "greengrass-core"]

---

# AWS IoT 생태계와 서비스 관계 정리

## 🎯 핵심 포인트

AWS에서 IoT 시스템을 구축하는 경우 IoT Core를 중심으로 한 생태계에서, 기기의 네트워크 환경과 처리 요구사항에 따라 직접 연결 또는 Greengrass 엣지 컴퓨팅을 선택할 수 있다.

## 📝 설명

### AWS IoT 서비스 구조와 관계

AWS IoT 생태계는 IoT Core를 중심 허브로 하여 모든 IoT 기기와 서비스가 연결되는 구조입니다. IoT Core는 클라우드 기반의 완전 관리형 서비스로서 MQTT 브로커, 기기 인증, 메시지 라우팅 기능을 제공하며, 모든 IoT 워크로드의 시작점이 됩니다. IoT Greengrass는 엣지 컴퓨팅을 위한 소프트웨어 패키지로, 로컬 디바이스에서 실행되어 오프라인 동작과 실시간 처리를 지원합니다.

### 서비스별 정의와 역할

**AWS IoT Core**: AWS 클라우드에서 실행되는 IoT 플랫폼으로, 수십억 개의 기기 연결을 지원하는 MQTT 브로커와 기기 관리 기능을 제공합니다.

**MQTT 프로토콜**: IoT에 최적화된 경량 메시징 프로토콜로, publish/subscribe 패턴을 사용하여 불안정한 네트워크에서도 효율적인 통신을 지원합니다.

**AWS IoT Greengrass**: 로컬 디바이스에서 실행되는 엣지 컴퓨팅 소프트웨어로, AWS Lambda 함수를 로컬에서 실행하고 클라우드와 동기화합니다.

**Greengrass Core**: Greengrass 소프트웨어의 핵심 런타임 엔진으로, 실제 엣지 디바이스에서 기기 관리와 로컬 처리를 담당합니다.

### 아키텍처 플로우

#### 패턴 1: 직접 클라우드 연결
```
IoT 기기 (MQTT 클라이언트) ←MQTT/인터넷→ AWS IoT Core ←Rules Engine→ AWS 서비스들
```

#### 패턴 2: 엣지 컴퓨팅 연결
```
IoT 기기 ←로컬통신→ Greengrass Core ←MQTT/인터넷→ AWS IoT Core ←Rules Engine→ AWS 서비스들
                         ↓
                   Local Lambda 함수
```

#### AWS IoT 생태계 전체 구조
```
┌─────────────────────────────────────────┐
│    IoT Analytics │ IoT Events │ SiteWise │
├─────────────────────────────────────────┤
│              AWS IoT Core               │  ← 중심 허브
├─────────────────────────────────────────┤
│ Greengrass │ Device Mgmt │ Device Defender │
├─────────────────────────────────────────┤
│              IoT 기기들                  │
└─────────────────────────────────────────┘
```

### Trade-offs 고려사항

**직접 IoT Core 연결 장점**:
- 단순한 아키텍처로 빠른 구현
- 완전 관리형으로 운영 부담 최소화
- 자동 확장으로 수백만 기기 지원
- 통합된 보안 및 인증 체계

**직접 IoT Core 연결 단점**:
- 항상 인터넷 연결 필요
- 실시간 처리 제한
- 네트워크 지연시간 존재

**Greengrass + IoT Core 장점**:
- 오프라인에서도 동작 가능
- 실시간 로컬 처리 지원
- 네트워크 대역폭 절약
- 지연시간 최소화

**Greengrass + IoT Core 단점**:
- 복잡한 아키텍처
- 로컬 디바이스 리소스 필요
- 엣지 디바이스 관리 복잡성

## 🔍 주요개념

### 서비스 구분

- **AWS IoT Core**: 클라우드 기반 IoT 플랫폼, 모든 IoT 서비스의 중심 허브
- **AWS IoT Greengrass**: 엣지 컴퓨팅 소프트웨어 패키지, 로컬 처리 및 오프라인 동작
- **Greengrass Core**: Greengrass의 실제 런타임 엔진, 로컬 디바이스에서 실행
- **MQTT**: IoT Core와 기기 간 통신을 위한 경량 프로토콜

### 연결 방식 선택 기준

**직접 연결을 선택하는 경우**:
- 안정적인 인터넷 연결 환경
- 단순한 데이터 수집 및 전송
- 실시간 처리가 중요하지 않음
- 운영 복잡성 최소화 필요

**Greengrass를 선택하는 경우**:
- 불안정하거나 제한적인 인터넷 연결
- 밀리초 단위의 실시간 처리 필요
- 오프라인에서도 동작해야 함
- 로컬에서 데이터 전처리 필요

### 실전 적용

- **풍력 터빈 모니터링**: 불안정한 네트워크 환경에서 IoT Core 직접 연결로 텔레메트리 데이터 수집
- **스마트 팩토리**: Greengrass를 통한 실시간 품질 검사 및 생산 라인 제어
- **자율주행차**: Greengrass Core에서 실시간 센서 데이터 처리 후 클라우드 동기화

## 📝 관련 문제

**Question:** An energy company has wind turbines, weather stations, and solar panels that produce telemetry data scattered across the city with spotty internet access. The organization wants predictive maintenance with scalable, secure, high-velocity data ingestion to Amazon S3. Which solution meets these requirements?

**Options:**

- A) Ingest data using HTTP API calls to EC2 instances with Auto Scaling behind ELB
- B) Ingest data over MQTT to AWS IoT Core, set up rules to use Kinesis Data Firehose to send data to Kinesis Data Stream configured for S3
- C) Ingest data over MQTT to AWS IoT Core, set up rules to direct MQTT data to Kinesis Data Firehose delivery stream configured for S3
- D) Ingest data over MQTT directly to Kinesis Data Stream configured for S3
- E) Deploy IoT Greengrass on each device for local processing then sync to IoT Core

**정답: C) AWS IoT Core + MQTT + Kinesis Data Firehose**

💡 추가 설명:

- **옵션 A** - HTTP는 IoT 기기에게 무겁고 불안정한 네트워크에서 연결 유지 어려움
- **옵션 B** - 불필요하게 복잡한 아키텍처, Firehose→Data Stream 구조는 비표준적
- **옵션 D** - IoT Core 없이는 MQTT 브로커와 기기 관리 기능 부족
- **옵션 E** - 단순 데이터 수집에는 과도한 복잡성, 직접 연결로 충분

---