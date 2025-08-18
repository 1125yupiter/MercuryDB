---

title: autonomous-sagemaker-edge-realtime
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker-neo
- service/greengrass
- constraint/real-time
- usecase/autonomous-vehicle
- deployment/edge-computing
aliases: ["Neo-Greengrass", "Edge-ML", "autonomous-ml"]

---

# 자율주행차 엣지 ML 처리 - SageMaker Neo와 Greengrass 조합

## 🎯 핵심 포인트

자율주행차에서 초당 수천 개의 도로 표지판 이미지를 최소 지연시간으로 분류해야 하는 경우, SageMaker Neo와 Greengrass를 조합한 엣지 컴퓨팅에서 네트워크 왕복 없이 10ms 이하로 실시간 처리할 수 있다.

## 📝 설명

### SageMaker Neo + Greengrass가 자율주행차 ML에 적합한 이유

자율주행차의 실시간 의사결정에는 극도로 낮은 지연시간이 필수적입니다. 클라우드 기반 처리는 네트워크 왕복으로 인해 200-500ms의 지연이 발생하지만, 엣지 컴퓨팅 조합은 이 문제를 근본적으로 해결합니다.

**SageMaker Neo**는 클라우드에서 훈련된 ML 모델을 엣지 디바이스용으로 최적화하는 컴파일러 역할을 합니다. 모델 크기를 최대 10분의 1까지 축소하고, 각 하드웨어 플랫폼에 최적화된 바이너리를 생성합니다.

**AWS IoT Greengrass**는 차량 내에서 최적화된 모델을 실제로 실행하는 엣지 컴퓨팅 플랫폼입니다. 인터넷 연결 없이도 ML 추론을 수행할 수 있어 네트워크 장애에도 안정적으로 작동합니다.

### 아키텍처 플로우

```
클라우드 단계:
SageMaker 모델 훈련 (500MB) → Neo 최적화 (50MB) → Greengrass 배포

차량 내 실시간 처리:
카메라 이미지 캡처 → Greengrass 로컬 추론 → <10ms 분류 결과 → 자율주행 시스템
```

### Trade-offs 고려사항

**SageMaker Neo + Greengrass 장점**:
- 극저지연 (<10ms) 실시간 처리
- 네트워크 의존성 제거로 안정성 확보
- 오프라인 상황에서도 지속 운영
- 대역폭 절약 및 클라우드 비용 감소
- 수천 대 차량 동시 배포 가능

**SageMaker Neo + Greengrass 단점**:
- 초기 설정 및 배포 복잡성
- 엣지 하드웨어 리소스 제약
- 모델 업데이트 시 OTA 배포 필요

**Rekognition + Lambda 장점**:
- 빠른 프로토타이핑 가능
- 서버리스 자동 스케일링
- 인프라 관리 부담 없음

**Rekognition + Lambda 단점**:
- 도로 표지판 전용 모델 부재
- 네트워크 지연으로 실시간 요구사항 충족 어려움
- 초당 수천 개 이미지 처리 한계

## 🔍 주요개념

### 핵심 역할 비교

- **Neo**: SageMaker에서 훈련된 큰 모델을 엣지용으로 압축/최적화하는 "모델 다이어트" 도구
- **Greengrass**: 차량 내에서 Neo가 만든 작은 모델을 실제로 실행하는 "엣지 컴퓨팅 OS"

### 실전 적용

- **자율주행**: 도로 표지판, 신호등, 장애물 실시간 인식
- **산업용 IoT**: 제조 라인에서 불량품 실시간 검출
- **의료 기기**: 환자 모니터링 디바이스에서 이상 징후 즉시 감지

## 📝 관련 문제

**Question:** An autonomous vehicle technology company is seeking an AWS solution capable of classifying street sign images with minimal latency, handling thousands of images each second. Which AWS services would most effectively fulfill this requirement?

**Options:**

- A) Amazon Rekognition, Lambda, IoT Core
- B) Amazon SageMaker, ECS, EC2 Spot Instances  
- C) Amazon SageMaker Ground Truth, Rekognition Custom Labels, Lambda, S3, DeepLens
- D) Amazon SageMaker, Neo, Greengrass
- E) Amazon Comprehend, Kinesis, Lambda

**정답: D) Amazon SageMaker, Neo, Greengrass**

💡 추가 설명:

- **Option A** - Rekognition은 도로 표지판 전용 모델이 아니며, 클라우드 기반 처리로 네트워크 지연 발생
- **Option B** - 클라우드 중심 처리 방식으로 네트워크 지연이 실시간 요구사항 충족에 부적합  
- **Option C** - 과도하게 복잡한 아키텍처로 서비스 간 조정 지연 및 DeepLens 하드웨어 제약
- **Option E** - Comprehend는 텍스트 분석 서비스로 이미지 분류에 부적합