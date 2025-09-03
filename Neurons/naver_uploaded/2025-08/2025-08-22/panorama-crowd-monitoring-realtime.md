---

title: panorama-crowd-monitoring-realtime
created: 2025-08-18
modified: 2025-08-18
tags:
- service/panorama
- constraint/real-time
- deployment/edge-computing
- usecase/crowd-monitoring
- technique/computer-vision
aliases: ["AWS Panorama", "edge-cv", "crowd-counter"]

---

# AWS Panorama를 활용한 실시간 군중 모니터링 시스템

## 🎯 핵심 포인트

기존 IP 카메라 네트워크에서 실시간 군중 모니터링이 필요한 경우 AWS Panorama에서, 로컬 추론을 통해 저지연 인원 카운팅과 임계값 기반 알림을 구현할 수 있다.

## 📝 설명

### AWS Panorama가 군중 모니터링에 적합한 이유

AWS Panorama는 기존 IP 카메라를 활용하여 엣지에서 컴퓨터 비전 추론을 수행하는 머신러닝 어플라이언스입니다. 비디오 데이터를 클라우드로 전송하지 않고 현장에서 직접 처리하여 높은 정확도와 낮은 지연시간을 제공합니다. 특히 대역폭이 제한적이거나 인터넷 연결이 불안정한 환경에서도 안정적인 운영이 가능하며, 실시간 의사결정이 중요한 안전 모니터링 시나리오에 최적화되어 있습니다.

### 아키텍처 플로우

```
기존 IP 카메라 → AWS Panorama 디바이스 → 로컬 CV 모델 추론
     ↓               ↓                  ↓
비디오 스트림        프레임 처리           인원 카운팅
     ↓               ↓                  ↓
실시간 모니터링    ← 로컬 커맨드 센터 ← 임계값 검증
     ↓
AWS SNS → SMS 알림 (임계값 초과시)
```

### Trade-offs 고려사항

**AWS Panorama 장점**:
- 기존 카메라 인프라 재사용으로 비용 절감
- 로컬 추론으로 실시간 응답 보장
- 네트워크 대역폭 절약 및 프라이버시 보호
- 오프라인 환경에서도 작동 가능

**AWS Panorama 단점**:
- 초기 하드웨어 설치 비용
- 로컬 디바이스 관리 및 유지보수 필요
- 복잡한 모델의 경우 하드웨어 성능 제약

**Kinesis Video Streams + SageMaker 장점**:
- 확장성이 뛰어난 클라우드 기반 처리
- 다양한 ML 모델 활용 가능
- 중앙집중식 관리 편의성

**Kinesis Video Streams + SageMaker 단점**:
- 높은 네트워크 대역폭 요구사항
- 클라우드 의존성으로 인한 지연시간 증가
- 지속적인 데이터 전송 비용

## 🔍 주요개념

### 엣지 컴퓨팅 vs 클라우드 추론

- **엣지 컴퓨팅**: 데이터가 생성되는 현장에서 직접 처리하여 지연시간 최소화
- **클라우드 추론**: 중앙화된 고성능 인프라에서 처리하여 확장성과 유연성 제공

### 실전 적용

- 공항이나 역사에서 승객 대기열 모니터링 및 혼잡도 관리
- 소매점에서 고객 밀도 분석을 통한 매장 운영 최적화
- 제조업체에서 작업장 안전 규정 준수 모니터링

## 📝 관련 문제

**Question:** To enforce safety protocols, a village is looking to integrate artificial intelligence (AI) into its existing IP cameras to monitor areas where large crowds gather. The AI model should be able to give a count of people in a given area in near-real time. The video feeds will be monitored and analyzed in a local command station. Furthermore, personnel manning the station must be alerted via SMS if the headcount exceeds the specified threshold. Which solution best fits the given requirements?

**Options:**

- A) Use Kinesis Data Streams to ingest video feeds. Create a Lambda-based consumer that calls a SageMaker face detection model to make near-real-time inferences. Configure the function to respond to S3 PUT events. Use the AWS SDK for Amazon SNS to send SMS messages.
- B) Use Kinesis Video Streams to ingest video feeds from the existing camera network. Create a Fargate application that processes and decodes frames from the stream. Send the decoded frames to a face detection model hosted on Amazon SageMaker. Use the AWS SDK for Amazon SNS to send SMS messages.
- C) Connect an AWS DeepLens camera to the existing camera network to consume video feeds. Deploy a people counter application built on top of a pre-trained face detection model. Use the AWS SDK for Amazon SNS to send SMS messages.
- D) Connect an AWS Panorama device to the existing camera network. Deploy a computer vision (CV) application to the device. Use the AWS SDK for Amazon SNS to send SMS messages.

**정답: D) AWS Panorama device with CV application**

💡 추가 설명:

- **옵션 A** - Kinesis Data Streams는 비디오 데이터 스트리밍에 적합하지 않으며, S3 PUT 이벤트 기반 처리는 실시간 요구사항에 맞지 않음
- **옵션 B** - 기술적으로 가능하나 로컬 추론 요구사항을 충족하지 못하고 네트워크 대역폭과 지연시간 문제 발생
- **옵션 C** - AWS DeepLens는 다른 카메라의 비디오 피드를 스트리밍하거나 처리할 수 없는 독립형 디바이스