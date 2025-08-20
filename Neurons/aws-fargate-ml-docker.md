---

title: serverless-fargate-ml-docker
created: 2025-08-20
modified: 2025-08-20
tags:
- service/fargate
- deployment/serverless
- container/docker
- usecase/machine-learning
- constraint/custom-libraries
aliases: ["fargate-ml", "serverless-container", "docker-fargate"]

---

# AWS Fargate를 활용한 서버리스 ML Docker 컨테이너 배포

## 🎯 핵심 포인트

커스텀 라이브러리가 필요한 ML 애플리케이션을 서버리스 환경에서 실행해야 하는 경우 AWS Fargate에서, 커스텀 태스크 정의를 생성하여 Docker 컨테이너 이미지를 배포할 수 있다.

## 📝 설명

### AWS Fargate가 ML Docker 컨테이너 배포에 적합한 이유

AWS Fargate는 서버리스 컨테이너 플랫폼으로, 인프라 관리 없이 Docker 컨테이너를 실행할 수 있습니다. 커스텀 라이브러리와 패키지가 포함된 ML 애플리케이션의 경우, 완전한 제어권을 가지면서도 서버리스의 이점을 누릴 수 있는 이상적인 솔루션입니다. Fargate 태스크 정의를 통해 컨테이너의 리소스 요구사항, 네트워크 구성, 환경 변수 등을 세밀하게 설정할 수 있습니다.

### 아키텍처 플로우

```
ML Docker Image → Amazon ECR → Fargate Task Definition → ECS Cluster
       ↓                ↓              ↓                    ↓
Custom Libraries → Image Registry → Container Blueprint → Serverless Runtime
       ↓                ↓              ↓                    ↓
  Dependencies  →   Secure Storage →  Resource Config  →  Auto-scaling
```

### Trade-offs 고려사항

**AWS Fargate 장점**:
- 완전한 서버리스 환경으로 인프라 관리 불필요
- 커스텀 Docker 이미지 완벽 지원
- 자동 스케일링과 로드 밸런싱
- 사용한 컴퓨팅 리소스에 대해서만 비용 지불
- 유연한 리소스 구성 (CPU, 메모리)

**AWS Fargate 단점**:
- EC2 대비 약간 높은 컴퓨팅 비용
- 콜드 스타트 지연시간 존재
- 로컬 스토리지 제한사항

**EC2 Instance 장점**:
- 완전한 제어권과 커스터마이제이션
- 장기 실행 시 비용 효율성

**EC2 Instance 단점**:
- 인프라 관리 책임 (패치, 모니터링, 스케일링)
- 서버리스가 아닌 전통적인 서버 관리 방식
- 운영 오버헤드 증가

**AWS Lambda 장점**:
- 진정한 이벤트 기반 서버리스
- 매우 빠른 스케일링

**AWS Lambda 단점**:
- Docker 컨테이너 지원 제한적
- 15분 최대 실행 시간 제한
- 10GB 메모리 제한으로 대용량 ML 모델 제약

**Elastic Beanstalk 장점**:
- 웹 애플리케이션 배포 단순화
- 다양한 플랫폼 지원

**Elastic Beanstalk 단점**:
- 서버리스가 아닌 PaaS 모델
- ML 컨테이너 배포에 특화되지 않음
- 인프라 관리 일부 필요

## 🔍 주요개념

### 서버리스 vs 전통적 배포

- **서버리스 (Fargate)**: 인프라 추상화, 자동 스케일링, 사용량 기반 과금
- **전통적 배포 (EC2)**: 직접적인 서버 관리, 수동 스케일링, 인스턴스 기반 과금

### 실전 적용

- 실시간 ML 추론 서비스를 위한 API 엔드포인트 배포
- 배치 처리용 ML 파이프라인의 컨테이너화된 워커 실행
- 다양한 ML 프레임워크가 혼재된 마이크로서비스 아키텍처 구현

## 📝 관련 문제

**Question:** You are building a machine learning application that requires running a custom Docker container image in a serverless environment on AWS. The Docker container image requires several custom libraries and packages that are not available in the default environment provided by AWS Fargate. Which of the following options is the most appropriate way to run the custom Docker container image in a serverless environment on AWS Fargate?

**Options:**

- A) Create an EC2 instance with the required custom libraries and packages, and deploy the Docker container image on the EC2 instance
- B) Use AWS Elastic Beanstalk to deploy the Docker container image in a serverless environment with the required custom libraries and packages
- C) Create an AWS Lambda function and package the custom libraries and packages with the function code, then deploy the Lambda function
- D) Create a custom Fargate task definition that includes the required custom libraries and packages, and deploy the Docker container image using the custom task definition

**정답: D) Create a custom Fargate task definition**

💡 추가 설명:

- **옵션 A (EC2 Instance)** - 서버리스 컴퓨팅의 목적에 부합하지 않으며 Fargate의 이점을 활용하지 못함
- **옵션 B (Elastic Beanstalk)** - 웹 애플리케이션 배포용 플랫폼으로 커스텀 Docker 컨테이너 실행에 특화되지 않음
- **옵션 C (AWS Lambda)** - Docker 컨테이너 실행에 설계되지 않았으며 장기 실행 작업에 부적합