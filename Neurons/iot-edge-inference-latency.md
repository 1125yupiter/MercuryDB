---

title: iot-edge-inference-latency
created: 2025-08-24
modified: 2025-08-24
tags:
- problem/latency
- service/iot-greengrass
- constraint/network-bandwidth
- usecase/quality-control
- deployment/edge-inference
aliases: ["edge-ml", "iot-inference", "manufacturing-ml"]

---

# IoT Edge ML 추론을 활용한 제조업 품질 관리 최적화

## 🎯 핵심 포인트

네트워크 대역폭이 제한된 제조 환경에서 수천 대의 기계가 실시간 이미지 품질 검사를 수행해야 하는 경우 AWS IoT Greengrass에서, 엣지 추론을 통해 지연 시간을 최소화할 수 있다.

## 📝 설명

### AWS IoT Greengrass가 제조업 엣지 추론에 적합한 이유

AWS IoT Greengrass는 엣지 디바이스에서 AWS Lambda 함수와 ML 모델을 로컬에서 실행할 수 있게 해주는 서비스입니다. 제조업 환경에서는 수천 대의 기계에서 초당 이미지를 생성하므로, 모든 데이터를 클라우드로 전송하면 네트워크 대역폭이 병목점이 됩니다. IoT Greengrass Core를 통해 각 산업용 PC에서 직접 추론을 실행하면 네트워크 트래픽을 대폭 줄이고 실시간 응답이 가능합니다.

### 아키텍처 플로우

```
[카메라] → [이미지 캡처] → [IoT Greengrass Core]
                                    ↓
[웹 서비스] ← [추론 결과] ← [엣지에서 Lambda + ML 모델 실행]
    ↓
[생산 라인 제어] (불량품 배송 방지)
```

### Trade-offs 고려사항

**IoT Greengrass 엣지 추론 장점**:
- 네트워크 대역폭 사용량 최소화 (추론 결과만 전송)
- 지연 시간 대폭 감소 (로컬 추론)
- 네트워크 장애 시에도 지속적 운영 가능
- 클라우드 비용 절감 (SageMaker 호출 횟수 감소)

**IoT Greengrass 엣지 추론 단점**:
- 각 디바이스별 모델 배포 및 관리 복잡성
- 산업용 PC의 컴퓨팅 리소스 제약
- 모델 업데이트 시 모든 엣지 디바이스 동기화 필요

**이미지 압축 솔루션 장점**:
- 기존 아키텍처 유지 가능
- 구현이 상대적으로 간단

**이미지 압축 솔루션 단점**:
- 여전히 네트워크 대역폭 사용 (압축해도 수천 대 기계에서는 한계)
- 압축/해제 과정에서 추가 지연 시간 발생
- 이미지 품질 손실 가능성

## 🔍 주요개념

### 엣지 vs 클라우드 추론 비교

- **엣지 추론**: 디바이스에서 직접 ML 모델 실행, 낮은 지연시간, 네트워크 독립성
- **클라우드 추론**: 중앙 집중식 모델 관리, 높은 컴퓨팅 성능, 네트워크 의존성

### 실전 적용

- 자동차 조립 라인에서 용접 품질 실시간 검사
- 반도체 제조에서 웨이퍼 결함 즉시 탐지
- 식품 가공에서 포장 불량 자동 분류

## 📝 관련 문제

**Question:** A manufacturing company uses machine learning models to detect quality defects. Models are built using images of products taken at the end of each production stage. Thousands of machines at the facility generate an average of one image per second. The company successfully piloted with one production unit, but when scaled to all machines, inference latency became unacceptable due to network bandwidth limits. What is the most cost-effective solution?

**Options:**

- A) Set up 10 Gbps AWS Direct Connect and increase SageMaker endpoint instances
- B) Compress images before uploading to S3 and decompress for inference
- C) Use SageMaker auto scaling with AWS Direct Connect connection
- D) Deploy Lambda functions and ML models on IoT Greengrass cores for edge inference
- E) Implement batch processing to reduce network calls

**정답: D) Deploy Lambda functions and ML models on IoT Greengrass cores for edge inference**

💡 추가 설명:

- **Option A** - Direct Connect는 고비용이며 근본적인 대역폭 문제를 해결하지 못함
- **Option B** - 수천 대 기계에서는 압축해도 네트워크 병목 지속, 품질 손실 위험
- **Option C** - 여전히 모든 이미지를 클라우드로 전송해야 하므로 대역폭 문제 미해결
- **Option E** - 실시간 품질 검사가 필요한 제조 환경에서 배치 처리는 부적합

---