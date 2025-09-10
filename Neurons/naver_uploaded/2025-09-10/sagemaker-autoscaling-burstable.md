---

title: sagemaker-autoscaling-burstable
created: 2025-08-27
modified: 2025-08-27
tags:
- service/sagemaker-endpoints
- constraint/cost-optimization
- deployment/auto-scaling
- instance/burstable
- problem/traffic-spikes
aliases: ["SageMaker Auto Scaling", "Burstable Instances", "Cost Optimization"]

---

# SageMaker 엔드포인트 트래픽 급증 대응을 위한 자동 확장 및 Burstable 인스턴스 활용

## 🎯 핵심 포인트

주기적인 트래픽 급증과 예측 가능한 사용 패턴을 가진 ML 추론 워크로드의 경우, InvocationsPerInstance 메트릭 기반 자동 확장과 T 시리즈 Burstable 인스턴스를 조합하여 성능을 보장하면서 비용을 최소화할 수 있다.

## 📝 설명

### 자동 확장과 Burstable 인스턴스가 트래픽 급증에 적합한 이유

**Auto Scaling with InvocationsPerInstance**는 실제 추론 요청 수를 모니터링하여 동적으로 인스턴스를 추가하거나 제거합니다. 특히 매월 초나 특정 시간대에 발생하는 예측 가능한 트래픽 급증에 효과적으로 대응할 수 있습니다.

**T 시리즈 Burstable 인스턴스**는 CPU 크레딧 시스템을 통해 기본 성능 수준에서 운영되다가 필요시 100% CPU까지 버스트할 수 있습니다. 평상시 낮은 사용률을 유지하며 크레딧을 축적하고, 트래픽 급증 시 축적된 크레딧을 소모하여 높은 성능을 제공합니다.

### 아키텍처 플로우

```
트래픽 급증 감지
    ↓
CloudWatch InvocationsPerInstance 메트릭 모니터링
    ↓
Auto Scaling 정책 트리거 (임계값 초과)
    ↓
새로운 T 시리즈 인스턴스 추가 생성
    ↓
기존 인스턴스: CPU 크레딧 소모하여 버스트 성능 제공
    ↓
트래픽 정상화 시 인스턴스 자동 축소
    ↓
크레딧 재축적 및 비용 절약
```

### Trade-offs 고려사항

**Auto Scaling + Burstable 조합 장점**:
- 실제 사용량에 따른 탄력적 확장으로 비용 최적화
- 예측 가능한 트래픽 패턴에 대한 자동 대응
- 크레딧 시스템을 통한 순간적 성능 향상
- Compute-optimized 대비 70-80% 비용 절약 가능

**Auto Scaling + Burstable 조합 단점**:
- 스케일 아웃 시 약간의 지연 시간 발생 (몇 분)
- 크레딧 소진 시 성능 제한 가능성
- 트래픽 패턴이 예측 불가능한 경우 비효율적

**다른 솔루션 장점**:
- **Multi-Model Endpoints**: 하나의 인스턴스에서 여러 모델 운영으로 리소스 효율성
- **Blue/Green 배포**: 무중단 배포 및 롤백 용이성
- **Serverless Inference**: 완전 관리형, 사용량 기반 과금

**다른 솔루션 단점**:
- **Elastic Inference**: 서비스 종료로 사용 불가
- **Production Variants**: 단순 A/B 테스팅용으로 확장성 문제 해결 불가
- **Blue/Green**: 배포 전략이지 트래픽 급증 해결책 아님

## 🔍 주요개념

### 핵심 기술 비교

- **T 시리즈 Burstable**: CPU 크레딧 기반으로 기본 성능(20%) 유지하다가 필요시 100% CPU까지 버스트. 웹 서버, 개발 환경, 간헐적 고성능이 필요한 워크로드에 적합
- **InvocationsPerInstance**: 인스턴스당 실제 추론 요청 수를 측정하는 CloudWatch 메트릭. CPU/Memory 기반보다 ML 워크로드에 더 정확한 확장 기준 제공
- **AWS Inferentia**: 추론 전용으로 설계된 AWS 자체 ML 칩. GPU 대비 최대 70% 비용 절약하지만 사전 컴파일 필요
- **Multi-Model Endpoints**: 단일 인스턴스에서 여러 모델을 동적으로 로딩/언로딩하여 리소스 효율성 극대화

### 실전 적용

- **E-commerce 추천 시스템**: 매월 초 급증, 주중 8AM-6PM 트래픽 패턴에 T3 인스턴스 + Auto Scaling 적용
- **뉴스 감정 분석 API**: 특정 이벤트 시 트래픽 급증하는 NLP 서비스에 Burstable + InvocationsPerInstance 메트릭 사용
- **금융 사기 탐지**: 거래량 증가 시점에 맞춰 자동 확장하는 실시간 추론 시스템

## 📝 관련 문제

**Question:** A company has an ecommerce website with a product recommendation engine built in TensorFlow. The recommendation engine endpoint is hosted by Amazon SageMaker. Three compute-optimized instances support the expected peak load of the website. Response times on the product recommendation page are increasing at the beginning of each month. Some users are encountering errors. The website receives the majority of its traffic between 8 AM and 6 PM on weekdays in a single time zone. Which of the following options are the MOST effective in solving the issue while keeping costs to a minimum? (Choose two.)

**Options:**

- A) Configure the endpoint to use Amazon Elastic Inference (EI) accelerators
- B) Create a new endpoint configuration with two production variants  
- C) Configure the endpoint to automatically scale with the InvocationsPerInstance metric
- D) Deploy a second instance pool to support a blue/green deployment of models
- E) Reconfigure the endpoint to use burstable instances

**정답: C) InvocationsPerInstance 자동 확장, E) Burstable 인스턴스 재구성**

💡 추가 설명:

- **옵션 A (Elastic Inference)** - AWS에서 서비스 종료된 기능으로 현재 사용 불가하며, 확장성 문제 해결 방안도 아님
- **옵션 B (Production Variants)** - A/B 테스팅이나 모델 비교용 기능으로 트래픽 급증 문제 해결과 무관하며 오히려 비용만 증가
- **옵션 D (Blue/Green 배포)** - 무중단 배포를 위한 전략으로 성능 문제나 확장성과는 직접적 관련 없음

---