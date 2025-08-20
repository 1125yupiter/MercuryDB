---

title: ecs-service-autoscaling-independent
created: 2025-08-20
modified: 2025-08-20
tags:
- service/ecs
- scaling/independent
- policy/target-tracking
- deployment/microservices
- monitoring/metrics
aliases: ["ecs-autoscaling", "service-scaling", "independent-scaling"]

---

# ECS Service Auto Scaling - 독립적 서비스 스케일링

## 🎯 핵심 포인트

ECS 클러스터에서 여러 서비스를 운영하는 경우 각 서비스의 고유 메트릭을 기반으로 독립적인 스케일링이 필요한 상황에서는, Service Auto Scaling with Target Tracking Scaling Policies를 사용할 수 있다.

## 📝 설명

### Service Auto Scaling이 독립적 스케일링에 적합한 이유

Service Auto Scaling은 각 ECS 서비스마다 별도의 스케일링 정책을 설정할 수 있어 서비스별 독립적 운영이 가능합니다. Target Tracking Scaling Policies를 통해 CPU 사용률, 메모리 사용률, ALB 요청 수, 사용자 정의 메트릭 등 각 서비스의 특성에 맞는 메트릭을 기반으로 자동 스케일링을 구현할 수 있습니다.

각 서비스는 자신만의 스케일링 정책과 임계값을 가지며, 다른 서비스의 상태나 메트릭에 영향받지 않고 독립적으로 스케일 인/아웃을 수행합니다. 이는 마이크로서비스 아키텍처에서 각 서비스의 부하 패턴이 다를 때 특히 유용합니다.

### 아키텍처 플로우

```
ECS Cluster
├── Service A (Web Frontend)
│   ├── Auto Scaling Policy: ALB Request Count
│   ├── Target Value: 1000 requests/task
│   └── Scale: 2-20 tasks
│
├── Service B (API Backend)  
│   ├── Auto Scaling Policy: CPU Utilization
│   ├── Target Value: 70%
│   └── Scale: 3-50 tasks
│
└── Service C (Data Processing)
    ├── Auto Scaling Policy: SQS Queue Length
    ├── Target Value: 30 messages/task
    └── Scale: 1-10 tasks

CloudWatch Metrics → Auto Scaling Policies → ECS Service Tasks
```

### Trade-offs 고려사항

**Service Auto Scaling 장점**:

- 각 서비스별 독립적인 스케일링 정책 설정 가능
- 다양한 메트릭(CPU, 메모리, 사용자 정의) 기반 스케일링
- Target Tracking을 통한 정확한 임계값 관리
- CloudWatch와 완전 통합된 모니터링

**Service Auto Scaling 단점**:

- 초기 설정의 복잡성
- 각 서비스별 적절한 메트릭과 임계값 선정 필요
- Cold start 지연으로 인한 초기 응답 시간 증가

**ECS Cluster Auto Scaling 장점**:

- 클러스터 레벨 용량 관리
- EC2 인스턴스 자동 프로비저닝

**ECS Cluster Auto Scaling 단점**:

- 서비스별 독립적 스케일링과는 목적이 다름
- 개별 서비스 메트릭을 직접 반영하지 못함

## 🔍 주요개념

### 스케일링 정책 비교

- **Target Tracking**: 특정 메트릭 값을 목표로 하여 자동으로 스케일 조정
- **Step Scaling**: 임계값 범위에 따라 단계적으로 스케일 조정
- **Simple Scaling**: 단일 임계값 기반 스케일 조정

### 실전 적용

- **웹 애플리케이션**: ALB 요청 수를 기반으로 프론트엔드 서비스 스케일링
- **API 서버**: CPU 사용률 기반으로 백엔드 API 서비스 스케일링  
- **배치 처리**: SQS 큐 길이를 기반으로 데이터 처리 서비스 스케일링

## 📝 관련 문제

**Question:** You have an Amazon ECS cluster running multiple services. You want to ensure that each service can scale independently based on its own metrics. What should you use to achieve this?

**Options:**

- A) Amazon ECS cluster auto scaling
- B) AWS Fargate capacity providers
- C) ECS task placement strategies
- D) Service Auto Scaling with target tracking scaling policies
- E) ECS service discovery

**정답: D) Service Auto Scaling with target tracking scaling policies**

💡 추가 설명:

- **Amazon ECS cluster auto scaling** - 클러스터 레벨의 용량 관리로, 개별 서비스의 독립적 스케일링과는 다른 목적
- **AWS Fargate capacity providers** - Fargate의 서버리스 용량 관리이며, 서비스별 메트릭 기반 스케일링과는 별개
- **ECS task placement strategies** - 태스크 배치 최적화 전략으로 스케일링 기능이 아님
- **ECS service discovery** - 서비스 간 통신을 위한 서비스 발견 기능으로 스케일링과 무관