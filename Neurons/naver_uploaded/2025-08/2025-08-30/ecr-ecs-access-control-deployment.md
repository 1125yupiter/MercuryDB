---

title: ecr-ecs-access-control-deployment
created: 2025-08-20
modified: 2025-08-20
tags:
- service/ecr
- service/ecs
- security/access-control
- deployment/container
- constraint/resource-level
aliases: ["ECR", "ECS", "container-security", "docker-registry", "access-control"]

---

# Amazon ECR 접근 제어를 통한 ECS ML 애플리케이션 보안 배포

## 🎯 핵심 포인트

ML 애플리케이션을 ECS에 배포하고 Docker 이미지를 ECR에 저장하는 경우, ECR의 리소스 레벨 권한 기능을 통해 특정 리포지토리나 이미지에 대한 세밀한 접근 제어를 구현할 수 있다.

## 📝 설명

### Amazon ECR이 컨테이너 이미지 보안 관리에 적합한 이유

Amazon ECR은 Docker 컨테이너 이미지를 위한 완전 관리형 레지스트리 서비스로, 리소스 레벨 권한을 지원하여 개별 리포지토리와 이미지에 대한 세밀한 접근 제어를 제공합니다. 기본적으로 모든 리포지토리와 이미지는 private으로 설정되며, IAM 정책과 리소스 기반 정책을 통해 인증된 사용자와 서비스만 접근할 수 있도록 보장합니다.

### 아키텍처 플로우

```
Developer → Docker Build → ECR Push → IAM Policy Check → Repository Access
    ↓                                        ↓
ML Application ← ECS Pull ← ECR Registry ← Resource-Level Permission
    ↓                           ↓
Production Deploy ← Task Role ← Cross-Account Access (Optional)
```

### Trade-offs 고려사항

**Amazon ECR 장점**:
- 리소스 레벨 권한으로 세밀한 접근 제어
- IAM과 완전 통합된 보안 모델
- 이미지 스캔 및 취약점 관리 기능
- ECS와 네이티브 통합

**Amazon ECR 단점**:
- AWS 생태계에 종속
- 초기 설정 시 IAM 정책 복잡성
- 리전별 복제 시 추가 비용

**Docker Hub 장점**:
- 퍼블릭 이미지 생태계
- 간단한 사용법

**Docker Hub 단점**:
- 기본 보안 수준이 낮음
- 엔터프라이즈 기능 제한
- AWS 서비스와의 통합 부족

## 🔍 주요개념

### 접근 제어 방식 비교

- **IAM 정책**: 사용자, 역할, 그룹에 대한 권한을 중앙에서 관리하며 AWS 전체 서비스와 일관된 보안 모델 제공
- **리소스 기반 정책**: 특정 ECR 리포지토리에 직접 연결된 정책으로 cross-account 접근 및 세밀한 권한 제어 가능
- **Security Groups**: 네트워크 레벨 접근 제어이며 ECR 자체 접근 제어와는 별개 (ECR은 HTTPS API 기반)

### 실전 적용

- **개발팀별 리포지토리 분리**: 각 팀이 자신의 ML 모델 이미지만 관리하고 배포할 수 있도록 권한 분리
- **환경별 접근 제어**: Dev, Staging, Production 환경의 이미지에 대해 서로 다른 접근 권한 설정
- **Cross-account 배포**: 중앙 ECR 계정에서 이미지를 관리하고 여러 운영 계정의 ECS에서 안전하게 pull

## 📝 관련 문제

**Question:** You are deploying a machine learning application on Amazon ECS and want to store Docker images in Amazon ECR. You need to ensure that images are securely stored and only accessible to authorized users. Which statement is true regarding ECR access control?

**Options:**

- A) Amazon ECR does not support resource-level permissions, and all users have access to all images in the registry by default
- B) Amazon ECR supports resource-level permissions, allowing you to grant or deny access to specific repositories or images
- C) Amazon ECR only supports access control via Security Groups, and IAM policies cannot be used for controlling access
- D) Amazon ECR only supports access control via AWS IAM policies, and does not allow you to create custom roles or policies
- E) Amazon ECR requires all images to be public by default for ECS integration

**정답: B) Amazon ECR supports resource-level permissions, allowing you to grant or deny access to specific repositories or images**

💡 추가 설명:

- **Option A** - ECR은 리소스 레벨 권한을 지원하며, 기본적으로 모든 리포지토리는 private으로 설정되어 인증된 사용자만 접근 가능
- **Option C** - ECR 접근 제어는 IAM 정책과 리소스 기반 정책을 통해 이루어지며, Security Groups는 네트워크 레벨 제어로 직접적인 ECR 접근 제어 방식이 아님
- **Option D** - ECR은 IAM 정책뿐만 아니라 사용자 정의 역할과 정책 생성을 지원하여 유연한 접근 제어 구현 가능
- **Option E** - ECR 이미지는 기본적으로 private이며, ECS 통합을 위해 public일 필요가 없음