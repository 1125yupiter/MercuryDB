---

title: sagemaker-autoscaling-inference
created: 2025-08-16 
modified: 2025-08-16 
tags:
- service/sagemaker
- constraint/high-traffic
- deployment/real-time
- scaling/auto-scaling
- usecase/sports-betting
aliases: ["sagemaker-scaling", "endpoint-autoscaling", "ml-inference-scaling"]

---

# SageMaker Auto-scaling for High-Traffic ML Inference

## 🎯 핵심 포인트

스포츠 베팅과 같이 트래픽이 급격히 변동하는 경우 SageMaker 엔드포인트에서, Auto-scaling을 구성하여 높은 확장성과 가용성을 확보할 수 있다.

## 📝 설명

### SageMaker Auto-scaling이 고트래픽 추론에 적합한 이유

SageMaker Auto-scaling은 실제 워크로드 변화에 따라 인스턴스 수를 동적으로 조정하는 기능입니다. 트래픽이 증가하면 더 많은 인스턴스를 온라인으로 전환하고, 워크로드가 감소하면 불필요한 인스턴스를 제거하여 비용을 절약합니다. 이는 스포츠 베팅처럼 경기 시간대에 트래픽이 급증하는 서비스에 특히 효과적입니다.

### 아키텍처 플로우

```
사용자 요청 
    ↓
Application Load Balancer
    ↓
SageMaker Endpoint (Auto-scaling enabled)
    ├── Instance 1 (기본)
    ├── Instance 2 (트래픽 증가 시)
    ├── Instance 3 (트래픽 증가 시)
    └── Instance N (최대 확장)
    ↓
CloudWatch Metrics (InvocationsPerInstance)
    ↓
Auto Scaling Policy (Target Tracking)
    ↓
스케일링 액션 (Scale Out/In)
```

### Trade-offs 고려사항

**SageMaker Auto-scaling 장점**:

- 실시간 트래픽 변화에 자동 대응
- 비용 효율성 (필요한 만큼만 리소스 사용)
- 고가용성 (다중 인스턴스로 단일 장애점 제거)
- 예측 불가능한 트래픽 패턴에 유연한 대응

**SageMaker Auto-scaling 단점**:

- 스케일링 시 약간의 지연 시간 발생 (몇 분)
- 복잡한 설정 및 모니터링 필요
- 콜드 스타트 문제 (새 인스턴스 초기화 시간)

**인스턴스 크기 증가 장점**:

- 즉시 적용 가능
- 설정이 단순함

**인스턴스 크기 증가 단점**:

- 수직 확장의 한계 존재
- 단일 장애점 문제
- 비용 비효율성 (항상 최대 리소스 사용)

## 🔍 주요개념

### 스케일링 방식 비교

- **수직 확장 (Scale Up)**: 인스턴스 크기를 증가시켜 더 많은 CPU/메모리 제공
- **수평 확장 (Scale Out)**: 인스턴스 개수를 증가시켜 로드 분산

### 실전 적용

- 스포츠 결승전처럼 예측 가능한 트래픽 급증 시나리오
- 실시간 사기 탐지 시스템에서 거래량 변동 대응
- 추천 시스템에서 특정 이벤트(세일, 신제품 출시) 시 트래픽 증가

## 📝 관련 문제

**Question:** A sports betting application relies on machine learning to predict match outcomes. These predictions are obtained by invoking an Amazon SageMaker endpoint. A machine learning engineer must ensure the SageMaker model stays available, even with a surge of traffic expected during an upcoming finals game. Which solution would offer the highest scalability to meet this requirement?

**Options:**

- A) Configure auto-scaling on the SageMaker endpoint
- B) Use Amazon SageMaker Neo to optimize the model for inference
- C) Increase the SageMaker's instance size to accommodate heavy traffic
- D) Host the SageMaker model in an AWS Inferentia-based instance

**정답: A) Configure auto-scaling on the SageMaker endpoint**

💡 추가 설명:

- **Option B (SageMaker Neo)** - 모델 최적화 도구일 뿐 확장성 문제를 해결하지 못함
- **Option C (인스턴스 크기 증가)** - 수직 확장에는 한계가 있고 단일 장애점 문제 존재
- **Option D (AWS Inferentia)** - 추론 성능 향상은 가능하지만 확장성 자체를 제공하지 않음