---

title: greengrass-edge-computing-offline
created: 2025-08-20
modified: 2025-08-20
tags:
- service/iot-greengrass
- deployment/edge-computing
- constraint/offline-capable
- usecase/local-processing
- feature/lambda-functions
aliases: ["greengrass", "gg", "edge-lambda"]

---

# AWS IoT Greengrass를 활용한 엣지 컴퓨팅 및 오프라인 Lambda 실행

## 🎯 핵심 포인트

네트워크 연결이 불안정하거나 실시간 응답이 필요한 IoT 환경에서, AWS IoT Greengrass를 사용하여 디바이스가 오프라인 상태에서도 Lambda 함수를 로컬 실행할 수 있다.

## 📝 설명

### AWS IoT Greengrass가 엣지 컴퓨팅에 적합한 이유

AWS IoT Greengrass는 AWS 클라우드의 기능을 엣지 디바이스로 확장하는 소프트웨어입니다. 핵심은 **하이브리드 아키텍처**로, 디바이스가 클라우드 연결 없이도 로컬에서 데이터를 처리하면서 동시에 클라우드의 관리 및 분석 기능을 활용할 수 있습니다. 특히 산업 현장, 원격지, 또는 네트워크가 불안정한 환경에서 실시간 의사결정이 필요한 경우에 매우 효과적입니다.

### 아키텍처 플로우

```
[IoT 센서] → [Greengrass Core] → [Local Lambda 실행] → [로컬 의사결정]
     ↓              ↓                    ↓
[데이터 수집]    [오프라인 처리]      [즉시 응답]
     ↓              ↓                    ↓
[클라우드 동기화] ← [배치 전송] ← [연결 복구시]
```

### Trade-offs 고려사항

**AWS IoT Greengrass 장점**:
- 오프라인 상태에서도 Lambda 함수 실행 가능
- 실시간 데이터 처리로 지연 시간 최소화
- 네트워크 대역폭 사용량 감소 (필요한 데이터만 클라우드 전송)
- 강력한 보안 기능 (종단 간 암호화, 로컬 인증)
- 다양한 하드웨어 플랫폼 지원 (Raspberry Pi, NVIDIA Jetson, x86)

**AWS IoT Greengrass 단점**:
- 초기 설정 및 구성의 복잡성
- 로컬 하드웨어 리소스 요구사항
- 디바이스별 관리 오버헤드
- 클라우드 대비 제한적인 컴퓨팅 성능

**AWS IoT Core 장점**:
- 간단한 설정 및 관리
- 무제한 클라우드 리소스 활용
- 중앙집중식 데이터 관리

**AWS IoT Core 단점**:
- 지속적인 인터넷 연결 필수
- 네트워크 지연으로 인한 응답 시간 증가
- 데이터 전송 비용 증가

## 🔍 주요개념

### 핵심 개념 비교

- **Greengrass Core**: 엣지 디바이스에서 실행되는 런타임 환경으로, Lambda 함수와 IoT 기능을 로컬에서 처리
- **Greengrass Group**: 여러 디바이스와 Lambda 함수, 리소스를 논리적으로 그룹화한 관리 단위
- **Local Lambda**: 클라우드 연결 없이 엣지 디바이스에서 직접 실행되는 AWS Lambda 함수
- **Device Shadow**: 디바이스 상태를 로컬에서 캐시하여 오프라인 상태에서도 상태 관리 가능

### 실전 적용

- **스마트 팩토리**: 제조 라인의 센서 데이터를 실시간 분석하여 품질 이상 즉시 감지 및 알림
- **자율주행차**: 차량 내 센서 데이터를 로컬에서 처리하여 밀리초 단위 의사결정 지원
- **농업 IoT**: 원격 농장의 환경 모니터링 및 자동 관개 시스템 제어 (인터넷 연결 불안정 환경)

## 📝 관련 문제

**Question:** A manufacturing company needs to deploy IoT sensors across multiple remote factory locations with unreliable internet connectivity. The sensors must process data locally and make real-time decisions for quality control, while periodically syncing with the cloud when connectivity is available. Which AWS service best meets these requirements?

**Options:**

- A) AWS IoT Core with persistent WebSocket connections
- B) Amazon Kinesis Data Streams with local buffering
- C) AWS IoT Greengrass with local Lambda functions
- D) AWS Lambda with API Gateway for device communication
- E) Amazon SQS with dead letter queues for reliability

**정답: C) AWS IoT Greengrass with local Lambda functions**

💡 추가 설명:

- **A) AWS IoT Core** - 지속적인 인터넷 연결이 필요하여 불안정한 네트워크 환경에 부적합
- **B) Amazon Kinesis Data Streams** - 로컬 데이터 처리 및 실시간 의사결정 기능을 제공하지 않음
- **D) AWS Lambda with API Gateway** - 클라우드 기반 솔루션으로 오프라인 환경에서 작동 불가
- **E) Amazon SQS** - 메시지 큐잉 서비스로 로컬 데이터 처리 및 실시간 분석 기능 없음