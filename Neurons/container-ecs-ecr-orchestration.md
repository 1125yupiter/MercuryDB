---

title: container-ecs-ecr-orchestration
created: 2025-08-21
modified: 2025-08-21
tags:
- service/ecs
- service/ecr
- deployment/container
- infrastructure/orchestration
- architecture/microservices
aliases: ["ECS", "ECR", "container-orchestration", "docker-registry", "aws-containers"]

---

# AWS ECS와 ECR을 활용한 컨테이너 오케스트레이션

## 🎯 핵심 포인트

컨테이너 기반 애플리케이션을 배포하는 경우 ECR에서 이미지를 저장 관리하고, ECS에서 실제 컨테이너 실행 및 오케스트레이션을 할 수 있다.

## 📝 설명

### AWS 컨테이너 서비스가 현대 애플리케이션 배포에 적합한 이유

Amazon ECS(Elastic Container Service)와 ECR(Elastic Container Registry)은 컨테이너 워크플로우에서 서로 다른 역할을 담당하는 상호 보완적인 서비스입니다. ECR은 Docker 이미지의 저장, 버전 관리, 보안 스캔을 담당하는 완전 관리형 프라이빗 레지스트리이며, ECS는 이러한 이미지를 실제 컨테이너로 실행하고 관리하는 오케스트레이션 서비스입니다.

### 아키텍처 플로우

```
개발자 → Docker Build → ECR Push → ECS Pull → Container Runtime
   ↓                      ↓            ↓            ↓
코드 작성              이미지 저장    이미지 검증   실제 서비스 실행
   ↓                      ↓            ↓            ↓
Git Push              버전 관리     태스크 정의    오토스케일링
   ↓                      ↓            ↓            ↓
CI/CD Pipeline        보안 스캔    서비스 배포    로드밸런싱
```

### Trade-offs 고려사항

**ECS 장점**:
- 완전 관리형 서비스로 인프라 관리 부담 없음
- EC2와 Fargate 두 가지 실행 모드 지원
- AWS 서비스들과의 원활한 통합 (ALB, CloudWatch, IAM)
- 비용 효율적인 스케일링
- 학습 곡선이 낮고 설정이 간단

**ECS 단점**:
- AWS 종속성으로 멀티 클라우드 전략 제한
- Kubernetes 대비 상대적으로 제한된 커뮤니티
- 복잡한 네트워킹 구성 시 학습 곡선
- 업계 표준이 아닌 AWS 고유 기술

**Kubernetes(EKS) 장점**:
- 표준화된 컨테이너 오케스트레이션 (업계 표준)
- 풍부한 생태계와 커뮤니티
- 멀티 클라우드 및 하이브리드 환경 지원
- 클라우드 중립적 기술

**Kubernetes(EKS) 단점**:
- 높은 복잡성과 관리 오버헤드
- 전문 지식 요구사항
- 초기 설정 및 운영 비용
- ECS 대비 AWS 서비스 통합의 복잡성

## 🔍 주요개념

### 핵심 서비스 비교

- **ECR (Elastic Container Registry)**: Docker 이미지를 저장하고 관리하는 프라이빗 레지스트리. 이미지 취약성 스캔, 라이프사이클 정책, IAM 기반 액세스 제어 제공
- **ECS (Elastic Container Service)**: 컨테이너를 실행하고 관리하는 완전 관리형 오케스트레이션 서비스. 태스크 정의, 서비스, 클러스터 개념으로 구성

### ECS vs Kubernetes 관계

**공통점**: 둘 다 컨테이너 오케스트레이션 플랫폼으로 동일한 목적 (컨테이너 배포, 스케일링, 로드밸런싱, 서비스 디스커버리)

**차이점**:
- **ECS**: AWS 고유의 완전 관리형 서비스, AWS 생태계 네이티브 통합
- **Kubernetes(EKS)**: 오픈소스 표준, 클라우드 중립적, 멀티클라우드 지원

**AWS 컨테이너 선택지**:
```
컨테이너 오케스트레이션 필요
         ↓
    ┌─────────┬─────────┬─────────┐
    ↓         ↓         ↓         ↓
   ECS      EKS    Fargate    ECS Anywhere
(AWS 고유) (관리형K8s) (서버리스) (온프레미스)
```

### 실전 적용

- **마이크로서비스 아키텍처**: 각 서비스별 독립적인 컨테이너 배포로 개발팀 간 독립성 확보
- **CI/CD 파이프라인**: ECR을 중심으로 한 이미지 빌드-테스트-배포 자동화
- **하이브리드 워크로드**: 레거시 애플리케이션과 신규 컨테이너 서비스의 점진적 통합
- **ECS 선택 시나리오**: AWS 생태계 의존 가능, 빠른 개발 우선, 오케스트레이션 초보자
- **Kubernetes(EKS) 선택 시나리오**: 멀티클라우드 전략, 업계 표준 기술, 복잡한 워크로드, K8s 전문 지식 보유

## 📝 관련 문제

**Question:** A company is deciding between ECS and EKS for their containerized application deployment. They need automatic scaling, want to minimize operational overhead, and plan to stay within AWS ecosystem. They also require integration with AWS services like ALB and CloudWatch. Which solution would be most appropriate?

**Options:**

- A) Use EKS (Elastic Kubernetes Service) for industry standard orchestration
- B) Use ECS with Fargate for simplified container management
- C) Deploy self-managed Kubernetes on EC2 instances
- D) Use ECS on EC2 with custom auto-scaling configuration
- E) Use Lambda functions with container runtime

**정답: B) Use ECS with Fargate for simplified container management**

💡 추가 설명:

- **Option A** - EKS는 더 복잡한 관리가 필요하고 운영 오버헤드가 높음
- **Option C** - 자체 관리 Kubernetes는 최대 운영 부담을 가져옴
- **Option D** - Fargate가 더 간단한 관리를 제공하므로 EC2보다 적합
- **Option E** - Lambda는 지속적 실행이 필요한 애플리케이션에 부적합