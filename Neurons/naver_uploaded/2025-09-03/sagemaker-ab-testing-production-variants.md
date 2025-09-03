---

title: sagemaker-ab-testing-production-variants
created: 2025-08-23
modified: 2025-08-23
tags:
- service/sagemaker-endpoints
- technique/ab-testing
- deployment/production-variants
- constraint/traffic-distribution
- usecase/model-comparison
aliases: ["A/B testing SageMaker", "production variants", "model comparison"]

---

# SageMaker A/B Testing - Production Variants를 통한 모델 비교 배포

## 🎯 핵심 포인트

여러 ML 모델 버전을 동시에 운영하며 성능을 비교해야 하는 경우 SageMaker Production Variants에서, 단일 엔드포인트로 트래픽 분산 비율을 조정하여 A/B 테스팅을 수행할 수 있다.

## 📝 설명

### SageMaker Production Variants가 A/B 테스팅에 적합한 이유

SageMaker Production Variants는 동일한 엔드포인트 뒤에서 여러 모델 버전을 호스팅하고, 각 모델에 대한 트래픽 분산 비율을 동적으로 조정할 수 있는 기능입니다. 이를 통해 최소한의 운영 오버헤드로 모델 성능을 실시간으로 비교하고, 점진적 배포(canary deployment) 전략을 구현할 수 있습니다.

### 아키텍처 플로우

```
Client Application
        ↓
SageMaker Endpoint (Single URL)
        ↓
Production Variant A (Weight: 70%) ← Model Version 1
        +
Production Variant B (Weight: 30%) ← Model Version 2
        ↓
Response with Model Performance Metrics
        ↓
CloudWatch Metrics & Monitoring
```

### Trade-offs 고려사항

**Production Variants 장점**:
- 단일 엔드포인트로 여러 모델 버전 관리
- 실시간 트래픽 분산 비율 조정 가능
- 자동화된 성능 메트릭 수집
- 점진적 모델 배포 전략 지원
- 최소한의 애플리케이션 코드 변경

**Production Variants 단점**:
- 동시 실행으로 인한 인프라 비용 증가
- 복잡한 모델 간 성능 차이 분석 필요
- 엔드포인트당 최대 10개 variants 제한

**Multiple Endpoints 장점**:
- 각 모델별 독립적인 리소스 관리
- 모델별 세밀한 성능 튜닝 가능

**Multiple Endpoints 단점**:
- 애플리케이션 레이어에서 복잡한 로직 구현 필요
- 트래픽 분산을 위한 추가적인 코드 개발
- 운영 복잡성 증가

## 🔍 주요개념

### A/B Testing vs Canary Deployment

- **A/B Testing**: 동일한 트래픽을 여러 모델 버전에 분산하여 성능 비교
- **Canary Deployment**: 새 모델에 소량의 트래픽부터 시작하여 점진적으로 증가

### 실전 적용

- **고객 이탈 예측 모델**: 기존 모델 70%, 새 모델 30%로 설정하여 비즈니스 임팩트 측정
- **추천 시스템**: 협업 필터링 50%, 딥러닝 기반 50%로 분산하여 클릭률 비교
- **이미지 분류 모델**: ResNet 40%, EfficientNet 60%로 정확도와 추론 속도 비교

## 📝 관련 문제

**Question:** A healthcare company is developing multiple machine learning models to predict patient readmission rates. The Data Science team wants to run several model versions in parallel for extended periods, with the ability to adjust the percentage of inferences served by each model to determine long-term performance. Which approach meets these requirements with the LEAST operational overhead?

**Options:**

- A) Create separate SageMaker endpoints for each model and implement traffic routing logic in the application layer
- B) Use SageMaker endpoint configuration with multiple production variants and programmatically adjust traffic weights
- C) Deploy models using SageMaker Neo with device-specific routing based on hospital equipment types  
- D) Create a single endpoint with SageMaker batch transform to control model invocation
- E) Use Lambda functions to route requests between different SageMaker endpoints

**정답: B) SageMaker endpoint configuration with multiple production variants**

💡 추가 설명:

- **Option A** - 애플리케이션 레이어에서 복잡한 라우팅 로직 구현 필요로 운영 오버헤드 증가
- **Option C** - SageMaker Neo는 모델 최적화 도구로 A/B 테스팅 목적에 부적합
- **Option D** - Batch Transform은 실시간 추론이 아닌 배치 처리용으로 요구사항 불일치
- **Option E** - Lambda를 통한 라우팅은 추가 레이턴시와 복잡성 초래