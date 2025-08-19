---

title: sagemaker-custom-container-deployment
created: 2025-08-19
modified: 2025-08-19
tags:
- service/sagemaker
- deployment/custom-container
- infrastructure/port-requirements
- constraint/real-time
- technique/containerization
aliases: ["SageMaker Custom Container", "BYOC SageMaker", "Custom Inference Container"]

---

# Amazon SageMaker 커스텀 컨테이너 배포 요구사항

## 🎯 핵심 포인트

커스텀 머신러닝 모델을 SageMaker에서 실시간 추론 서비스로 배포하는 경우, 포트 8080에서 /invocations와 /ping 엔드포인트를 구현하고 250ms 이내 소켓 연결 수락이 가능한 컨테이너를 사용할 수 있다.

## 📝 설명

### SageMaker가 커스텀 컨테이너 배포에 적합한 이유

Amazon SageMaker는 BYOC(Bring Your Own Container) 방식을 통해 사용자 정의 Docker 컨테이너를 지원합니다. 이를 통해 특정 프레임워크나 라이브러리가 필요한 복잡한 모델도 SageMaker의 관리형 인프라에서 배포할 수 있습니다. SageMaker는 표준화된 통신 프로토콜을 통해 컨테이너와 상호작용하며, 자동 스케일링, 로드 밸런싱, 모니터링 등의 기능을 제공합니다.

### 아키텍처 플로우

```
Client Request → SageMaker Endpoint → Custom Container (Port 8080)
                                   ↓
                                /ping (GET) - Health Check
                                   ↓
                              /invocations (POST) - Inference
                                   ↓
                            Model Processing & Response
                                   ↓
                              Client ← JSON Response
```

### Trade-offs 고려사항

**SageMaker 커스텀 컨테이너 장점**:
- 완전한 환경 제어 가능
- 기존 Docker 워크플로우 활용
- 복잡한 전처리/후처리 로직 구현 가능
- 다양한 프레임워크 지원

**SageMaker 커스텀 컨테이너 단점**:
- 컨테이너 빌드 및 관리 복잡성
- 표준 엔드포인트 구현 필수
- 디버깅이 상대적으로 어려움
- 초기 설정 비용 증가

**SageMaker 내장 알고리즘 장점**:
- 즉시 사용 가능
- 최적화된 성능
- 간단한 배포 과정

**SageMaker 내장 알고리즘 단점**:
- 제한된 커스터마이징
- 특정 프레임워크에만 국한
- 복잡한 비즈니스 로직 구현 어려움

## 🔍 주요개념

### 필수 엔드포인트 비교

- **`/ping` (GET)**: 컨테이너 상태 확인용 헬스체크 엔드포인트, HTTP 200과 빈 응답 반환, 2초 내 응답 필수
- **`/invocations` (POST)**: 실제 추론 요청을 처리하는 엔드포인트, 60초 내 응답 필수, 최대 25MB 페이로드 지원

### 실전 적용

- E-commerce 추천 시스템에서 복잡한 앙상블 모델 배포
- 의료 영상 분석을 위한 커스텀 CNN 모델 서빙
- 금융 사기 탐지를 위한 실시간 피처 엔지니어링 파이프라인 구축

## 📝 관련 문제

**Question:** A machine learning development team is deploying a custom machine learning model using Amazon SageMaker. The model has been containerized to facilitate deployment. To ensure successful deployment and operation of the custom container on Amazon SageMaker, which of the following requirements must be met by the container? (CHOOSE TWO)

**Options:**

- A) Must respond to /invocations and /ping requests on port 80
- B) Must accept all socket connection requests within 250 ms
- C) Must respond to /invocations and /ping requests on port 8080
- D) Must be compressed in ZIP format for deployment
- E) Must respond to GET requests on the /ping endpoint within 5 seconds

**정답: B, C) 250ms 소켓 연결 수락 & 포트 8080 엔드포인트 구현**

💡 추가 설명:

- **Option A** - SageMaker는 표준 HTTP 포트 80이 아닌 포트 8080을 사용합니다
- **Option D** - 모델 아티팩트는 ZIP이 아닌 tar.gz 형식으로 압축되어야 합니다  
- **Option E** - /ping 엔드포인트는 5초가 아닌 2초 이내에 응답해야 합니다