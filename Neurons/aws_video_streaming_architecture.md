---
title: video-streaming-kinesis-realtime-security
created: 2025-08-28
modified: 2025-08-28
tags:
- service/kinesis-video-streams
- constraint/real-time
- usecase/security-monitoring
- deployment/fargate
- problem/video-analytics
aliases: ["kvs", "video-stream", "realtime-video"]
---

# AWS 실시간 비디오 스트리밍 보안 감시 시스템

## 🎯 핵심 포인트

실시간 비디오 스트림을 처리해야 하는 경우 Kinesis Video Streams에서, 커스텀 머신러닝 모델과 연동하여 보안 이벤트를 탐지할 수 있다.

## 📝 설명

### Kinesis Video Streams가 실시간 비디오 처리에 적합한 이유

**핵심 오해 해결**: AWS Rekognition은 단독으로 실시간 비디오 스트림을 직접 처리할 수 없다. Rekognition Video Stream Processor를 사용하려면 반드시 Kinesis Video Streams가 입력 소스로 필요하다.

**Kinesis Video Streams의 작동 방식**:
- 공항, CCTV 등에서 오는 실시간 비디오 스트림을 직접 수신
- 스트림 데이터를 실시간으로 여러 처리 워커에게 분배
- SageMaker 커스텀 모델과의 원활한 연동 지원
- 처리 결과를 다시 스트림으로 전달하여 후속 처리 가능

### 아키텍처 플로우

```
공항 CCTV 카메라들
       ↓
Kinesis Video Streams (실시간 스트림 수집)
       ↓
ECS Fargate 컨테이너들 (비디오 분석 워커)
       ↓
SageMaker 커스텀 모델 (보안 이벤트 탐지)
       ↓
Kinesis Data Streams (알림 데이터 스트림)
       ↓
Lambda 함수 (알림 처리)
       ↓
SNS (최종 알림 발송)
```

### ECS Fargate의 역할

**Fargate = "이미 모든 설정이 완료된 컨테이너 실행 환경"**:
- EC2 인스턴스 관리 불필요 (서버 패치, 보안, 네트워크 설정 자동화)
- 비디오 처리량에 따른 자동 스케일링
- 사용한 컴퓨팅 리소스만큼만 과금 (비용 효율적)
- GPU 지원으로 머신러닝 추론 작업 가능

### Trade-offs 고려사항

**Kinesis Video Streams + Fargate 장점**:
- 실시간 스트리밍 데이터 직접 수신 가능
- 커스텀 SageMaker 모델 자유로운 활용
- 원본 비디오 데이터를 SageMaker에 훈련 데이터로 전송 가능
- 서버리스 환경으로 운영 부담 최소화
- 트래픽 변동에 따른 자동 확장/축소

**Kinesis Video Streams + Fargate 단점**:
- 초기 설정이 복잡할 수 있음
- 커스텀 모델 개발 및 유지보수 필요
- 비용 예측이 어려울 수 있음 (트래픽 변동에 따라)

**AWS Rekognition 장점**:
- 즉시 사용 가능한 사전 훈련된 모델
- 별도 모델 개발 불필요
- 안정적인 성능 보장

**AWS Rekognition 단점**:
- 실시간 스트리밍을 위해서도 Kinesis Video Streams 필요
- 사전 정의된 기능만 사용 가능 (커스터마이징 제한적)
- NSA 같은 특수 보안 요구사항에 부적합할 수 있음
- 원본 데이터를 SageMaker 훈련용으로 활용하기 어려움

## 🔍 주요개념

### 서비스별 특성 비교

- **Kinesis Video Streams**: 실시간 비디오 스트림 수집 및 저장 전용 서비스, 다른 AWS 서비스와의 연동 허브 역할
- **AWS Rekognition**: 이미지/비디오 분석 AI 서비스, 실시간 처리를 위해서는 Kinesis Video Streams와 연동 필수
- **ECS Fargate**: 서버리스 컨테이너 실행 환경, EC2 인스턴스 관리 없이 애플리케이션 실행
- **SageMaker**: 커스텀 머신러닝 모델 개발, 훈련, 배포 플랫폼

### 실전 적용

- **공항 보안 시스템**: 실시간 CCTV 모니터링으로 의심스러운 행동 패턴 탐지
- **스마트 시티**: 교통 상황 실시간 분석 및 사고 감지
- **제조업 품질 관리**: 생산 라인 실시간 모니터링으로 불량품 즉시 탐지

## 📝 관련 문제

**Question:** You work as a machine learning specialist for the department of defense in the NSA. The NSA is responsible for security in the ports of entry around the United States. You need to process real-time video streams from airports around the country to identify questionable activity within the airport facilities and send the streaming data to SageMaker to be used as training data for your model. Your model needs to trigger an alert system when a security event is detected. What AWS services would you use to create this system most accurately and cost-effectively?

**Options:**

A) Use AWS Rekognition to process your video streams and send the processed data to your SageMaker model. When the model detects a security event, a lambda function is triggered to publish an SNS message to the alert system.

B) Use AWS Elastic Transcoder to process the video streams and send the processed data to your SageMaker model. When the model detects a security event, a lambda function is triggered to publish an SNS message to the alert system.

C) Use Amazon Kinesis Video Streams to stream the video to a set of processing workers running in ECS Fargate. The workers send the video data to your SageMaker machine learning model which identifies alert situations. These alerts are processed by Kinesis Data Streams which uses a lambda function to trigger the alert system.

D) Use Amazon Kinesis Data Streams to process your video data using lambda functions which push out an SNS notification to the alert system when a security event is detected.

**정답: C) Amazon Kinesis Video Streams + ECS Fargate + SageMaker**

💡 추가 설명:

- **Option A (AWS Rekognition)** - Rekognition만으로는 실시간 비디오 스트림을 직접 받을 수 없음. Kinesis Video Streams가 선행되어야 하며, 커스텀 SageMaker 모델 활용에 제약이 있음
- **Option B (Elastic Transcoder)** - 비디오 포맷 변환 서비스로 실시간 스트리밍이나 머신러닝 분석 목적에 부적합
- **Option D (Kinesis Data Streams)** - 일반 데이터 스트리밍에는 적합하지만 비디오 데이터 처리에는 Kinesis Video Streams가 더 특화되어 있음