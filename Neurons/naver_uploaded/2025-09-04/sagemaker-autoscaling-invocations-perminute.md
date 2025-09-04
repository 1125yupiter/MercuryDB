---

title: sagemaker-autoscaling-invocations-perminute
created: 2025-01-23
modified: 2025-01-23
tags:
- service/sagemaker-endpoints
- deployment/auto-scaling
- metric/invocations-per-instance
- constraint/safety-factor
- monitoring/per-minute
aliases: ["sagemaker-scaling", "endpoint-scaling", "invocations-metric"]

---

# SageMaker Endpoint Auto Scaling에서 InvocationsPerInstance 설정 방법

## 🎯 핵심 포인트

로드 테스트를 통해 RPS를 확인한 경우 SageMaker Auto Scaling에서, 안전 계수를 적용하고 분당 기준으로 변환하여 InvocationsPerInstance를 설정할 수 있다.

## 📝 설명

### SageMaker Auto Scaling이 InvocationsPerInstance 메트릭에 적합한 이유

SageMaker의 Auto Scaling은 CloudWatch 메트릭을 기반으로 인스턴스를 자동으로 확장/축소합니다. `InvocationsPerInstance` 메트릭은 각 인스턴스가 처리하는 요청 수를 분당 기준으로 측정하여, 트래픽 패턴에 따라 적절한 스케일링 결정을 내릴 수 있게 합니다.

### 계산 프로세스 플로우

```
로드 테스트 결과 (RPS)
         ↓
안전 계수 적용 (Safety Factor)
         ↓
실제 사용할 RPS 계산
         ↓
분당 기준으로 변환 (×60초)
         ↓
InvocationsPerInstance 설정값
```

### Trade-offs 고려사항

**Auto Scaling 장점**:
- 트래픽에 따른 자동 인스턴스 관리
- 비용 최적화 (필요한 만큼만 리소스 사용)
- 예측 불가능한 트래픽 패턴에 대응

**Auto Scaling 단점**:
- 스케일 아웃 시 약간의 지연 시간 발생
- Cold Start 문제 가능성
- 복잡한 메트릭 설정 필요

**Manual Scaling 장점**:
- 예측 가능한 성능
- 즉시 사용 가능한 리소스

**Manual Scaling 단점**:
- 리소스 낭비 가능성
- 수동 관리 부담
- 예상치 못한 트래픽 급증 시 대응 어려움

## 🔍 주요개념

### 핵심 개념 비교

- **RPS (Requests Per Second)**: 초당 처리 가능한 요청 수로 성능 벤치마크의 기준
- **Safety Factor**: 실제 최대 성능 대비 안전 여유분을 두는 계수 (0.5 = 50% 여유분)
- **InvocationsPerInstance**: SageMaker에서 인스턴스당 분당 처리하는 추론 요청 수

### 실전 적용

- 첫 번째 프로덕션 배포 시 안전 계수 0.5-0.7 적용하여 안정성 확보
- 트래픽 패턴이 예측 가능한 경우 더 높은 안전 계수 사용 고려
- 실시간 모니터링을 통해 실제 성능과 설정값의 차이 분석 및 조정

## 📝 관련 문제

**Question:** A Machine Learning Specialist is tasked with determining the optimal SageMakerVariantInvocationsPerInstance setting for an endpoint's automated scaling configuration. The Specialist conducted a load test on a single instance and concluded that the maximum number of requests per second (RPS) without degrading service is around 20 RPS. Due to the fact that this is the Specialist's first deployment, he wishes to set the invocation safety factor to 0.5. What should the Specialist put as the SageMakerVariantInvocationsPerInstance setting based on the aforementioned parameters and the fact that the invocations per instance setting is monitored on a per-minute basis?

**Options:**
- A) 10
- B) 30  
- C) 600
- D) 2,400

**정답: C) 600**

💡 추가 설명:

- **옵션 A (10)** - 안전 계수만 적용하고 분당 변환을 누락한 값
- **옵션 B (30)** - 계산 근거가 불분명한 값
- **옵션 D (2,400)** - 안전 계수 미적용 후 분당 변환한 값 (20 RPS × 60초 × 2)

**계산 과정:**
1. 최대 RPS × 안전 계수 = 20 × 0.5 = 10 RPS
2. 분당 변환: 10 RPS × 60초 = 600 invocations per minute