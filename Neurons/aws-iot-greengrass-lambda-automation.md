---

title: aws-iot-greengrass-lambda-automation
created: 2025-08-21
modified: 2025-08-21
tags:
- service/greengrass
- service/lambda
- deployment/edge
- automation/custom-logic
- architecture/serverless
aliases: ["greengrass", "edge-lambda", "iot-automation"]

---

# AWS IoT Greengrass와 Lambda를 활용한 엣지 자동화 아키텍처

## 🎯 핵심 포인트

엣지 디바이스에서 커스텀 로직이 필요한 경우 AWS IoT Greengrass에서, Lambda 함수를 통해 클라우드 개발 환경의 편의성과 엣지 실행의 이점을 동시에 얻을 수 있다.

## 📝 설명

### AWS IoT Greengrass가 엣지 자동화에 적합한 이유

AWS IoT Greengrass는 클라우드의 서버리스 컴퓨팅을 엣지 디바이스로 확장하는 서비스입니다. 핵심은 **"클라우드에서 개발하고 엣지에서 실행하는"** 하이브리드 모델입니다.

**Core vs Device 구조**:
- **Core**: AWS Lambda 함수를 실행하는 소프트웨어 컴포넌트 (프로그램 덩어리)
- **Device**: Core가 설치되어 실행되는 물리적 하드웨어 (기기)

**Lambda 기반 실행 모델**:
엣지 디바이스에서 수행해야 하는 모든 작업(센서 데이터 읽기, 데이터 처리, 알림 발송 등)은 개별 Lambda 함수로 정의됩니다. 이는 모듈화된 개발과 중앙 집중식 관리를 가능하게 합니다.

### 아키텍처 플로우

```
AWS Lambda (클라우드 개발)
    ↓ 함수 정의 및 관리
Greengrass 배포 그룹
    ↓ 배포 대상 설정
엣지 디바이스 (Greengrass Core)
    ↓ 로컬 실행
센서 데이터 → [Lambda A: 읽기] → [Lambda B: 처리] → [Lambda C: 전송]
```

### AWS 완전관리형 vs 커스텀 로직

**완전관리형 서비스들**:
- RDS (데이터베이스), S3 (스토리지), CloudFront (CDN) 등
- 설정만으로 자동 운영, 코딩 불필요

**커스텀 로직이 필요한 순간**:
- "S3에 파일 업로드되면 → 특정 처리 수행"
- "센서 값이 임계치 초과하면 → 알림 발송"
- "API 요청이 오면 → 비즈니스 로직 실행"

이러한 **"특정 처리"** 부분이 모두 Lambda 함수로 구현됩니다.

### Trade-offs 고려사항

**AWS IoT Greengrass 장점**:
- 클라우드 개발 환경의 편의성 (풍부한 도구, 중앙 관리)
- 오프라인 상태에서도 엣지 실행 가능
- 여러 디바이스 동시 업데이트 및 버전 관리
- 모듈화된 Lambda 함수로 재사용성 높음

**AWS IoT Greengrass 단점**:
- 모든 기능을 Lambda 함수로 개별 정의해야 함
- 초기 설정 복잡성
- AWS 생태계 종속성

**직접 엣지 개발 장점**:
- 하드웨어 최적화 가능
- 외부 종속성 없음

**직접 엣지 개발 단점**:
- 개발 환경 구축 복잡
- 디바이스별 개별 관리 필요
- 업데이트 배포 어려움

## 🔍 주요개념

### 레고 블럭 아키텍처

- **AWS 서비스들**: 완성된 레고 블럭 (입력과 출력이 정의된 관리형 서비스)
- **Lambda 함수**: 블럭들을 연결하는 접착제 (커스텀 로직)

### 현대적 개발 방식

- **요구사항 정의**: 입력부, 출력부, 원하는 작업 명확화
- **LLM 활용 개발**: ChatGPT, Claude 등으로 Lambda 코드 생성
- **검증 및 배포**: 생성된 코드 테스트 후 Greengrass로 배포

### 실전 적용

- **스마트 팩토리**: 온도 센서 모니터링 → 임계값 초과 시 자동 알림
- **농업 IoT**: 토양 습도 측정 → 자동 관개 시스템 제어
- **물류 관리**: RFID 태그 스캔 → 재고 업데이트 및 보고서 생성

## 📝 관련 문제

**Question:** A manufacturing company needs to process sensor data locally on edge devices while maintaining centralized management of the processing logic. The solution should allow offline operation and easy updates across multiple devices. What AWS service combination best addresses these requirements?

**Options:**

- A) AWS IoT Core with EC2 instances for edge processing
- B) AWS IoT Greengrass with Lambda functions for edge logic
- C) Amazon Kinesis Data Streams with local data collectors
- D) AWS IoT Device Management with custom applications
- E) Amazon SageMaker Edge with pre-trained models only

**정답: B) AWS IoT Greengrass with Lambda functions for edge logic**

💡 추가 설명:

- **Option A** - EC2 인스턴스는 완전관리형이 아니며 개별 서버 관리 필요
- **Option C** - Kinesis는 스트리밍 서비스로 엣지 로컬 처리에 부적합
- **Option D** - 디바이스 관리만 제공하며 로컬 처리 로직 실행 불가
- **Option E** - SageMaker Edge는 ML 추론 전용으로 범용 센서 처리에 제한적