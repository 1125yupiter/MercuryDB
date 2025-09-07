---
title: edge-ml-restaurant-queue-counting
created: 2025-08-24
modified: 2025-08-24
tags:
- constraint/limited-bandwidth
- deployment/edge-computing
- service/sagemaker-custom
- usecase/queue-counting
- hardware/panorama-appliance
aliases: ["edge-ml-queue", "restaurant-counting", "bandwidth-constrained-ml"]
---

# 제한된 대역폭 환경에서 엣지 ML을 활용한 레스토랑 줄 카운팅

## 🎯 핵심 포인트

제한된 대역폭 환경에서 실시간 줄 카운팅이 필요한 경우, SageMaker 커스텀 모델을 엣지 디바이스에 배포하여 로컬 추론 후 알림만 전송할 수 있다.

## 📝 설명

### 엣지 ML이 대역폭 제약 환경에 적합한 이유

레스토랑과 같은 제한된 네트워크 환경에서는 비디오 스트리밍이 다른 업무에 지장을 줄 수 있습니다. 엣지 컴퓨팅 접근법은 다음과 같이 작동합니다:

1. **모델 훈련**: SageMaker에서 줄 서기 상황에 특화된 커스텀 모델 개발
2. **엣지 배포**: 훈련된 모델을 현장의 엣지 디바이스에 배포
3. **로컬 추론**: 비디오 처리와 사람 수 카운팅을 현장에서 수행
4. **선택적 알림**: 임계값 초과 시에만 작은 크기의 알림 메시지 전송

### 아키텍처 플로우

```
클라우드 (SageMaker)              엣지 디바이스 (현장)
┌─────────────────┐              ┌──────────────────────┐
│ 모델 훈련       │   배포        │ 실시간 영상 캡처     │
│ - 줄서기 데이터 │  ────→       │ ↓                   │
│ - 커스텀 모델   │              │ 로컬 추론 (사람 수)  │
└─────────────────┘              │ ↓                   │
                                 │ Lambda 함수 실행     │
                                 │ ↓                   │
                                 │ 임계값 체크          │
                                 │ ↓                   │
                                 │ SNS 알림 (필요시만)  │
                                 └──────────────────────┘
```

### Trade-offs 고려사항

**엣지 ML (SageMaker + Edge Device) 장점**:
- 극도로 제한된 대역폭 사용 (알림만 전송)
- 실시간 처리로 즉각적인 반응
- 네트워크 단절 시에도 계속 동작
- 특정 환경에 최적화된 높은 정확도

**엣지 ML 단점**:
- 초기 설정 복잡성
- 하드웨어 구매 및 유지보수 비용
- 모델 업데이트 시 현장 방문 필요 가능성

**클라우드 스트리밍 (Kinesis Video Streams) 장점**:
- 중앙집중식 관리
- 쉬운 모델 업데이트
- 데이터 축적 및 분석 용이

**클라우드 스트리밍 단점**:
- 높은 대역폭 요구사항 (제약사항 위반)
- 네트워크 의존성
- 지연 시간 발생

## 🔍 주요개념

### 엣지 디바이스 비교

- **AWS DeepLens (서비스 종료)**: 카메라와 컴퓨팅이 통합된 올인원 장치, 단일 카메라만 지원
- **AWS Panorama**: 별도 컴퓨팅 장치로 최대 20대의 IP 카메라 연결 가능, 상업용 환경에 적합

### 실전 적용

- **단일 계산대 레스토랑**: DeepLens 또는 Panorama 모두 적용 가능
- **다중 계산대 체인점**: Panorama가 여러 카메라 통합 관리로 효율적
- **대역폭이 극도로 제한된 원격지**: 엣지 솔루션이 유일한 실용적 선택

## 📝 관련 문제

**Question:** A quick-service restaurant chain wants to implement a line-counting application using video cameras pointed at customer queues. The restaurants have limited bandwidth for external connections and cannot support multiple video streams without affecting other operations. Which machine learning solution should be adopted?

**Options:**

A) Install cameras compatible with Amazon Kinesis Video Streams to stream data to AWS. Write a Lambda function to capture images and send them to Amazon Rekognition for face counting. Send SNS notifications when lines are too long.

B) Deploy AWS DeepLens cameras in restaurants to capture video. Enable Amazon Rekognition on DeepLens devices to trigger local Lambda functions when people are recognized. Use Lambda to send SNS notifications when lines are too long.

C) Build a custom model in Amazon SageMaker to recognize people in images. Install cameras compatible with Kinesis Video Streams. Write a Lambda function to capture images and use the SageMaker endpoint for counting. Send SNS notifications when lines are too long.

D) Build a custom model in Amazon SageMaker to recognize people in images. Deploy AWS DeepLens cameras in restaurants. Deploy the model to the cameras and use local Lambda functions to count people and send SNS notifications when lines are too long.

E) Use Amazon Rekognition Video API with real-time streaming from multiple cameras to count people and automatically trigger CloudWatch alarms when thresholds are exceeded.

**정답: D) SageMaker Custom Model + DeepLens Edge Deployment**

💡 추가 설명:

- **Option A** - Kinesis Video Streams는 지속적인 비디오 스트리밍으로 대역폭 제약사항 위반
- **Option B** - 범용 Rekognition보다 특화된 커스텀 모델이 줄 카운팅에 더 정확
- **Option C** - 비디오 스트리밍과 클라우드 추론으로 대역폭 집약적
- **Option E** - 실시간 스트리밍으로 가장 높은 대역폭 요구사항

**현실적 고려사항**: AWS DeepLens는 2024년 1월 서비스 종료되어, 실제로는 AWS Panorama나 다른 엣지 솔루션을 고려해야 함