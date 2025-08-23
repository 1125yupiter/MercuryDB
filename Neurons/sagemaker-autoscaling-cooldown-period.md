---

title: sagemaker-autoscaling-cooldown-period
created: 2025-08-23
modified: 2025-08-23
tags:
- service/sagemaker-endpoints
- constraint/autoscaling
- performance/cooldown-period
- deployment/production
- problem/premature-scaling
aliases: ["sagemaker-cooldown", "autoscaling-delay", "endpoint-scaling"]

---

# SageMaker 엔드포인트 자동 스케일링에서 쿨다운 기간 조정을 통한 조기 스케일링 방지

## 🎯 핵심 포인트

프로덕션 SageMaker 엔드포인트에서 새 인스턴스가 사용 가능하기 전에 추가 인스턴스가 시작되는 경우, 스케일 아웃 활동의 쿨다운 기간을 늘려서 불필요한 조기 스케일링을 방지할 수 있다.

## 📝 설명

### SageMaker 자동 스케일링이 조기 스케일링 문제에 적합한 해결책인 이유

SageMaker 엔드포인트의 자동 스케일링에서 쿨다운 기간은 스케일링 활동 간의 대기 시간을 제어하는 핵심 매개변수입니다. 새 인스턴스가 완전히 초기화되어 트래픽을 처리할 준비가 되기 전에 추가 인스턴스가 시작되는 문제는 쿨다운 기간이 너무 짧아서 발생합니다. 이는 리소스 낭비와 비용 증가로 이어집니다.

### 아키텍처 플로우

```
트래픽 증가 감지
       ↓
스케일링 메트릭 확인 (CPU/Memory/Custom)
       ↓
스케일 아웃 결정
       ↓
새 인스턴스 시작
       ↓
[쿨다운 기간 대기] ← 여기서 기간 조정 필요
       ↓
인스턴스 완전 준비 상태 확인
       ↓
추가 스케일링 평가
```

### Trade-offs 고려사항

**쿨다운 기간 증가 장점**:
- 불필요한 인스턴스 생성 방지
- 비용 효율성 개선
- 안정적인 스케일링 동작
- 리소스 오버프로비저닝 방지

**쿨다운 기간 증가 단점**:
- 급격한 트래픽 증가 시 응답 지연 가능성
- 스케일링 반응성 감소
- 피크 트래픽 처리 능력 일시적 제한

**다른 대안들의 한계**:
- **API Gateway + Lambda**: 기존 스케일링 문제 해결하지 못함
- **스케일 인 쿨다운 감소**: 문제와 반대 방향의 조치
- **Multi-model endpoint**: 스케일링 타이밍 문제와 무관

## 🔍 주요개념

### 스케일링 쿨다운 비교

- **Scale-out Cooldown**: 인스턴스 추가 후 다음 스케일 아웃까지의 대기 시간
- **Scale-in Cooldown**: 인스턴스 제거 후 다음 스케일 인까지의 대기 시간

### 실전 적용

- ML 모델 초기화 시간이 긴 경우 쿨다운 기간을 모델 로딩 시간보다 길게 설정
- 트래픽 패턴이 예측 가능한 경우 적절한 쿨다운으로 안정성 확보
- 비용 최적화가 중요한 프로덕션 환경에서 오버프로비저닝 방지

## 📝 관련 문제

**Question:** A business used Amazon SageMaker hosting services to build up and deploy their machine learning model into production using an endpoint. The machine learning team has setup automated scaling for its SageMaker machines to accommodate changing workloads. The team sees that further instances are being started prior to the new instances being available. This conduct must be altered immediately. How can the machine learning team resolve this issue?

**Options:**

- A) Decrease the cooldown period for the scale-in activity. Increase the configured maximum capacity of instances.
- B) Replace the current endpoint with a multi-model endpoint using SageMaker.
- C) Set up Amazon API Gateway and AWS Lambda to trigger the SageMaker inference endpoint.
- D) Increase the cooldown period for the scale-out activity.

**정답: D) Increase the cooldown period for the scale-out activity.**

💡 추가 설명:

- **Option A** - 스케일 인 쿨다운 감소는 인스턴스 제거와 관련되어 문제 해결에 부적절하며, 최대 용량 증가는 근본 원인을 해결하지 못함
- **Option B** - Multi-model endpoint는 여러 모델을 하나의 엔드포인트에서 호스팅하는 기능으로 스케일링 타이밍 문제와 무관
- **Option C** - API Gateway와 Lambda 추가는 기존 자동 스케일링의 조기 시작 문제를 해결하지 못하고 아키텍처만 복잡하게 만듦