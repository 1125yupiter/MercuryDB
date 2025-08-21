---

title: ecs-task-definition-containers
created: 2025-08-21
modified: 2025-08-21
tags:
- service/ecs
- container/management
- deployment/orchestration
- architecture/microservices
- platform/aws
aliases: ["ECS Task Definition", "태스크 정의", "task-def"]

---

# Amazon ECS 태스크 정의를 통한 다중 컨테이너 오케스트레이션

## 🎯 핵심 포인트

여러 컨테이너를 하나의 애플리케이션으로 구성해야 하는 경우 Amazon ECS에서, 태스크 정의를 통해 다중 컨테이너를 하나의 태스크로 함께 실행할 수 있다.

## 📝 설명

### Amazon ECS 태스크 정의가 컨테이너 오케스트레이션에 적합한 이유

Amazon ECS 태스크 정의(Task Definition)는 하나 이상의 컨테이너가 함께 실행되는 방법을 정의하는 청사진입니다. 이는 마이크로서비스 아키텍처에서 서로 연관된 컨테이너들을 논리적 단위로 그룹화하여 관리할 수 있게 해줍니다. 각 컨테이너의 리소스 할당, 네트워크 설정, 환경 변수, 볼륨 마운트 등을 세밀하게 제어할 수 있으며, EC2와 Fargate 두 플랫폼에서 모두 사용 가능합니다.

### 아키텍처 플로우

```
태스크 정의 생성
    ↓
컨테이너 1 (nginx) ← 포트 80 노출
    ↓
컨테이너 2 (php-fpm) ← 내부 통신
    ↓
컨테이너 3 (redis) ← 캐시 레이어
    ↓
ECS 서비스로 배포
    ↓
로드밸런서 연결
```

### Trade-offs 고려사항

**ECS 태스크 정의 장점**:
- 여러 컨테이너를 하나의 논리적 단위로 관리 가능
- 컨테이너 간 네트워크 및 볼륨 공유 간소화
- EC2와 Fargate 모두에서 사용 가능
- AWS 네이티브 서비스와 완벽한 통합

**ECS 태스크 정의 단점**:
- 모든 컨테이너가 같은 호스트에서 실행되어 단일 실패 지점 존재
- 개별 컨테이너 스케일링이 제한적
- AWS 종속적 설정

**Kubernetes Pod 장점**:
- 더 풍부한 오케스트레이션 기능
- 멀티 클라우드 호환성
- 커뮤니티 생태계

**Kubernetes Pod 단점**:
- 복잡한 설정과 관리 오버헤드
- AWS 네이티브 서비스와의 통합 복잡성

## 🔍 주요개념

### 핵심 개념 비교

- **태스크(Task)**: 태스크 정의를 기반으로 실행되는 컨테이너들의 인스턴스
- **서비스(Service)**: 지정된 수의 태스크를 유지하고 로드밸런싱을 제공하는 상위 개념
- **태스크 정의(Task Definition)**: 컨테이너 구성의 청사진, 버전 관리 가능

### 실전 적용

- **웹 애플리케이션**: Nginx + PHP-FPM + Redis를 하나의 태스크로 구성
- **데이터 파이프라인**: 데이터 수집 컨테이너 + 처리 컨테이너 + 모니터링 컨테이너 조합
- **마이크로서비스**: API 서버 + 사이드카 프록시 + 로깅 에이전트 패턴

## 📝 관련 문제

**Question:** A company is deploying a web application that consists of a web server, application server, and caching layer. They want these components to run together and share the same network and storage resources. Which Amazon ECS feature should they use?

**Options:**

- A) Create separate task definitions for each component and deploy them independently
- B) Use a single task definition that specifies multiple containers for all components
- C) Deploy each component as a standalone ECS service
- D) Use ECS container instances with manual container deployment
- E) Implement each component as a separate Lambda function

**정답: B) Use a single task definition that specifies multiple containers for all components**

💡 추가 설명:

- **선택지 A** - 컴포넌트 간 네트워크 및 스토리지 공유가 복잡해짐
- **선택지 C** - 개별 서비스로 분리하면 tight coupling이 필요한 컴포넌트 관리가 어려움
- **선택지 D** - ECS의 자동화된 오케스트레이션 기능을 활용하지 못함
- **선택지 E** - Lambda는 장기 실행 웹 애플리케이션에 적합하지 않음