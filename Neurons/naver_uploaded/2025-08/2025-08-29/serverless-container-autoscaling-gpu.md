---

title: serverless-container-autoscaling-gpu
created: 2025-08-20
modified: 2025-08-20
tags:
- deployment/serverless
- service/fargate
- service/sagemaker
- constraint/gpu-support
- scaling/auto
aliases: ["serverless-ml", "container-gpu", "fargate-vs-sagemaker"]

---

# 서버리스 컨테이너 환경에서 GPU 지원 ML 애플리케이션 배포

## 🎯 핵심 포인트

서버리스 환경에서 Docker 컨테이너로 자동 스케일링이 필요한 경우 AWS Fargate를 사용하고, GPU 지원이 추가로 필요한 경우 Amazon SageMaker Endpoints를 활용할 수 있다.

## 📝 설명

### AWS Fargate가 서버리스 컨테이너 배포에 적합한 이유

AWS Fargate는 완전 서버리스 컨테이너 실행 엔진으로, 인프라 관리 없이 Docker 컨테이너를 배포할 수 있다. Application Auto Scaling과 통합하여 CPU/메모리 사용률 기반 자동 스케일링을 지원하며, 실제 사용한 vCPU와 메모리에 대해서만 과금한다.

### 아키텍처 플로우

```
Traffic → ALB → ECS Service (Fargate) → Auto Scaling
                     ↓
              Container Tasks ←→ CloudWatch Metrics
                     ↓
              Scale Up/Down based on CPU/Memory
```

### GPU 요구사항이 있는 경우의 대안

AWS Fargate는 GPU를 지원하지 않으므로, GPU가 필요한 ML 워크로드의 경우 다른 접근 방식이 필요하다:

```
ML Inference Request → SageMaker Endpoint → GPU Instance
                              ↓
                    Auto Scaling (Instance-based)
                              ↓
                    Custom Container (BYOC)
```

### Trade-offs 고려사항

**AWS Fargate 장점**:
- 완전 서버리스 (인프라 관리 불필요)
- 자동 스케일링 지원
- 종량제 과금으로 비용 효율적
- 표준 Docker 컨테이너 완벽 지원

**AWS Fargate 단점**:
- GPU 지원 없음
- 영구 스토리지 제한
- Cold start 지연시간
- 특정 네트워킹 요구사항 제한

**SageMaker Endpoints 장점**:
- GPU 인스턴스 지원 (p3, p4, g4 등)
- ML에 특화된 기능들 (A/B 테스팅, 모델 모니터링)
- 자동 스케일링 지원
- 사용자 정의 컨테이너 지원 (BYOC)

**SageMaker Endpoints 단점**:
- 완전 서버리스는 아님 (최소 인스턴스 유지)
- Fargate 대비 높은 비용
- ML 이외 용도에는 과도한 기능

## 🔍 주요개념

### 서버리스 vs 관리형 서비스 비교

- **서버리스 (Fargate)**: 인프라 완전 추상화, 사용량 기반 과금, 0까지 스케일 다운 가능
- **관리형 (ECS EC2)**: 인스턴스 관리 필요, 예약 인스턴스로 비용 절약 가능, GPU 지원

### 실전 적용

- **웹 애플리케이션 API**: Fargate + ALB로 트래픽 기반 자동 스케일링
- **GPU 기반 실시간 추론**: SageMaker Endpoints + GPU 인스턴스
- **배치 ML 작업**: AWS Batch + GPU 지원 EC2 인스턴스

## 📝 관련 문제

**Question:** You are designing a machine learning application that requires deploying Docker containers in a serverless environment. The application needs to scale up and down automatically based on the incoming traffic. Which AWS service is most appropriate for this requirement?

**Options:**

- A) Amazon ECS (EC2 launch type)
- B) AWS Fargate
- C) AWS Lambda
- D) Amazon EKS
- E) Amazon SageMaker Endpoints

**정답: B) AWS Fargate**

💡 추가 설명:

- **Amazon ECS (EC2 launch type)** - 컨테이너 지원하지만 서버리스 환경이 아님 (EC2 인스턴스 관리 필요)
- **AWS Lambda** - 서버리스이지만 실행 시간 제한(15분)과 패키지 크기 제한으로 ML 애플리케이션에 부적합
- **Amazon EKS** - Kubernetes 관리형 서비스이지만 완전 서버리스가 아니며 클러스터 관리 오버헤드 존재
- **Amazon SageMaker Endpoints** - GPU 지원하지만 ML 전용이며 완전 서버리스는 아님 (최소 인스턴스 유지 필요)