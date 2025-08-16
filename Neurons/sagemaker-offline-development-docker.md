---

title: sagemaker-offline-development-docker
created: 2025-08-16
modified: 2025-08-16
tags:
- service/sagemaker
- constraint/offline-development
- technique/docker-containers
- deployment/local-mode
- problem/internet-disruption
aliases: ["sagemaker-local", "offline-ml", "docker-ml"]

---

# SageMaker 오프라인 개발 - Docker 컨테이너 기반 로컬 모드

## 🎯 핵심 포인트

인터넷 연결이 불안정한 환경에서 SageMaker 기반 ML 프로젝트를 계속 개발해야 하는 경우, TensorFlow Docker 컨테이너를 로컬로 pull하고 SageMaker Python SDK를 설치하여 완전한 오프라인 개발 환경을 구축할 수 있다.

## 📝 설명

### SageMaker Docker 컨테이너가 오프라인 ML 개발에 적합한 이유

SageMaker는 사전 구축된 TensorFlow와 MXNet 컨테이너를 제공하여 로컬 훈련을 지원한다. 이 컨테이너들은 SageMaker 클라우드 환경과 완전히 동일한 실행 환경을 로컬에서 재현할 수 있게 해준다. 모든 라이브러리 의존성, 환경 설정, 그리고 런타임 구성이 포함되어 있어 "works on my machine" 문제를 해결하고, 개발에서 운영까지 일관된 환경을 보장한다.

### 아키텍처 플로우

```
SageMaker 클라우드 환경
         ↓
Docker 컨테이너 Pull
         ↓
로컬 머신에 컨테이너 저장
         ↓
SageMaker Python SDK 설치
         ↓
로컬 모드로 훈련/추론 실행
         ↓
코드 수정 ↔ 즉시 테스트
         ↓
클라우드 재배포 (인터넷 복구 후)
```

### Trade-offs 고려사항

**Docker 컨테이너 방식 장점**:
- 클라우드와 완전히 동일한 실행 환경
- 모든 의존성이 컨테이너에 포함되어 오프라인 작업 가능
- 코드 수정 후 즉시 테스트 및 디버깅
- 개발-운영 환경 간 일관성 보장
- SageMaker 로컬 모드로 실제 클라우드 동작 시뮬레이션

**Docker 컨테이너 방식 단점**:
- 초기 컨테이너 다운로드 시 큰 용량 (수 GB)
- 로컬 머신의 GPU/메모리 리소스 제약
- 분산 훈련 등 일부 클라우드 전용 기능 제한

**단순 TensorFlow 설치 장점**:
- 빠른 설치와 가벼운 환경
- 로컬 머신 리소스 효율적 사용

**단순 TensorFlow 설치 단점**:
- SageMaker 환경과 라이브러리 버전 차이로 인한 호환성 문제
- Eager execution만으로는 SageMaker 특화 기능 테스트 불가
- 개발 환경과 운영 환경 간 차이로 인한 예상치 못한 에러

## 🔍 주요개념

### 핵심 개념 비교

- **SageMaker 로컬 모드**: SageMaker SDK를 사용하여 로컬 머신에서 클라우드와 동일한 방식으로 훈련/추론을 실행하는 기능
- **Docker 컨테이너**: SageMaker에서 사용하는 사전 구축된 ML 실행 환경을 패키징한 이미지
- **Eager Execution**: TensorFlow에서 연산을 즉시 실행하는 프로그래밍 환경 (그래프 빌드 없이 즉시 결과 반환)

### 실전 적용

- **주말 긴급 개발**: 인터넷이 불안정한 환경에서 프로젝트 데드라인 맞추기
- **이동 중 개발**: 비행기나 오프라인 환경에서 모델 개발 및 디버깅
- **하이브리드 개발**: 로컬에서 빠른 반복 개발 후 클라우드에서 대규모 훈련

## 📝 관련 문제

**Question:** A Machine Learning Specialist is developing a proof-of-concept project involving TensorFlow running on an Amazon SageMaker AI instance. The Specialist plans to continue working remotely on weekends to meet the project's deadline. However, there are concerns that an internet service disruption might block development progress, including the loss of Wi-Fi access for an extended period of time. How can the Specialist continue working without Internet access?

**Options:**

- A) Install TensorFlow on a local machine and use TensorFlow's eager execution environment. Copy the TensorFlow code from the SageMaker AI instance.
- B) Create a virtual Python environment on his local machine and install boto3 to continue code development.
- C) Pull the TensorFlow Docker container into a local machine and execute the pip install -U sagemaker command to make use of the Amazon SageMaker Python SDK for code testing.
- D) Create an image of the SageMaker AI instance and convert it to a vmdk image format. Launch a virtual machine using the converted image in his local machine.

**정답: C) Pull the TensorFlow Docker container into a local machine and execute the pip install -U sagemaker command to make use of the Amazon SageMaker Python SDK for code testing.**

💡 추가 설명:

- **옵션 A** - Eager execution은 단순히 TensorFlow의 즉시 실행 환경일 뿐이며, SageMaker 특화 환경과 라이브러리 호환성 문제가 발생할 수 있음
- **옵션 B** - boto3는 AWS 서비스 제어용 SDK로 ML 모델 개발 환경이 아니며, 오프라인에서는 AWS API 호출이 불가능함
- **옵션 D** - SageMaker는 완전 관리형 서비스로 EC2 인스턴스의 직접적인 이미징 및 다운로드가 불가능함