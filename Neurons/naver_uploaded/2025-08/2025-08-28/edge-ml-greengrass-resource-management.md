---
title: edge-ml-greengrass-resource-management
created: 2025-08-19
modified: 2025-08-19
tags:
- service/greengrass
- deployment/edge
- constraint/resource-limited
- usecase/ml-inference
- architecture/distributed
aliases: ["greengrass-ml", "edge-inference", "iot-edge"]

---

# AWS IoT Greengrass를 활용한 엣지 디바이스 ML 추론 최적화

## 🎯 핵심 포인트

리소스가 제한된 엣지 디바이스에서 ML 추론을 효율적으로 실행해야 하는 경우, AWS IoT Greengrass Machine Learning과 Greengrass Core를 사용하여 Lambda 함수별 리소스 제한을 구성함으로써 메모리와 CPU 사용량을 최적화할 수 있다.

## 📝 설명

### AWS IoT Greengrass가 엣지 ML 배포에 적합한 이유

AWS IoT Greengrass는 엣지 디바이스에서 클라우드 기능을 로컬로 실행할 수 있게 해주는 서비스입니다. 특히 ML 추론의 경우, 클라우드에서 설정을 관리하고 엣지에서 실제 추론을 실행하는 하이브리드 아키텍처를 제공합니다.

**핵심 구성 요소별 역할:**

- **AWS IoT Greengrass (클라우드)**: Lambda 함수의 리소스 제한을 위한 스크립트/설정 입력 및 관리
- **Greengrass Core (엣지 디바이스)**: 클라우드에서 설정한 내용들을 실제 반영하여 엣지에서 추론 실행
- **SageMaker Neo**: 추론용 모델을 엣지 디바이스용으로 최적화/압축

### 아키텍처 플로우

```
AWS 클라우드 (Greengrass 서비스)
├── Lambda 함수 리소스 제한 설정
├── ML 모델 배포 관리
└── 디바이스 상태 모니터링
    ↓ 배포/설정 전송
엣지 디바이스 (Greengrass Core)
├── 설정된 리소스 제한 적용
├── ML 모델 로컬 추론 실행
├── Lambda 함수 실행
└── 오프라인 동작 지원
    ↑ 상태/결과 보고
SageMaker Neo
└── 모델 최적화 및 압축
```

### Trade-offs 고려사항

**AWS IoT Greengrass Machine Learning 장점**:
- Lambda 함수별 세밀한 리소스 제한 설정 가능
- 완전한 오프라인 추론 지원으로 지연시간 최소화
- 실시간 모델 업데이트 및 원격 관리
- 내장된 보안 및 디바이스 인증

**AWS IoT Greengrass Machine Learning 단점**:
- 초기 설정 복잡성
- 디바이스별 개별 관리 필요
- 제한된 하드웨어에서의 성능 최적화 필요

**Stream Manager + Lambda (제한 없음) 단점**:
- 리소스 경합으로 인한 성능 저하
- 메모리 누수 위험
- 디바이스 안정성 문제

**IoT Analytics 단점**:
- 클라우드 의존적 처리로 지연시간 증가
- 네트워크 연결 필수
- 개인정보 보호 이슈

## 🔍 주요개념

### 엣지 컴퓨팅 vs 클라우드 컴퓨팅

- **엣지 컴퓨팅**: 데이터 생성 지점 근처에서 처리, 낮은 지연시간, 오프라인 동작 가능
- **클라우드 컴퓨팅**: 중앙집중식 처리, 높은 컴퓨팅 파워, 네트워크 의존성

### 리소스 제한 vs 무제한 실행

- **리소스 제한**: 안정적 성능, 예측 가능한 동작, 다중 모델 동시 실행 가능
- **무제한 실행**: 단일 작업 최대 성능, 리소스 경합 위험, 시스템 불안정 가능성

### 실전 적용

- **제조업 품질 검사**: 실시간 결함 탐지를 위한 비전 AI 모델 배포
- **자율주행 차량**: 센서 데이터 기반 즉시 의사결정을 위한 ML 추론
- **스마트 시티**: IoT 센서 네트워크에서 이상 탐지 및 예측 분석

## 📝 관련 문제

**Question:** You are building a machine learning application that needs to run inference locally on edge devices. You have decided to use AWS IoT Greengrass to deploy and manage the machine learning models on edge devices. You want to ensure that the edge devices have the necessary resources to perform inference efficiently, while also limiting the memory and CPU utilization. What is the best way to achieve this?

**Options:**

- A) Use AWS IoT Greengrass Stream Manager to manage the data streaming from edge devices to the cloud, but use AWS Lambda to deploy machine learning models on edge devices without configuring the resource limits
- B) Use AWS IoT Greengrass to deploy machine learning models on edge devices without configuring the resource limits
- C) Use AWS IoT Analytics to deploy machine learning models on edge devices and configure the resource limits for each Lambda function
- D) Use AWS IoT Greengrass Machine Learning with AWS IoT Greengrass Core to deploy machine learning models on edge devices and configure the resource limits for each Lambda function

**정답: D) AWS IoT Greengrass Machine Learning with AWS IoT Greengrass Core**

💡 추가 설명:

- **Option A** - Stream Manager는 데이터 스트리밍 관리용이며, 리소스 제한 없는 Lambda는 성능 저하 위험
- **Option B** - 리소스 제한 없이 배포하면 메모리/CPU 과사용으로 디바이스 안정성 문제
- **Option C** - IoT Analytics는 클라우드 기반 분석 서비스로 엣지 추론에 부적합하며 지연시간 및 프라이버시 이슈 발생