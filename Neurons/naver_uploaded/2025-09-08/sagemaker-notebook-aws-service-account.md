---

title: sagemaker-notebook-aws-service-account
created: 2025-08-25
modified: 2025-08-25
tags:
- service/sagemaker
- architecture/managed-service
- constraint/hidden-infrastructure
- deployment/aws-account
- troubleshooting/visibility
aliases: ["sagemaker-notebook", "aws-service-account", "notebook-instance"]

---

# SageMaker 노트북 인스턴스 - AWS 서비스 계정 아키텍처

## 🎯 핵심 포인트

SageMaker 노트북 인스턴스를 사용하는 경우 AWS 서비스 계정에서 실행되는 EC2 인스턴스를 기반으로 하므로, 고객의 EC2 콘솔에서 해당 인스턴스나 EBS 볼륨을 직접 확인할 수 없다.

## 📝 설명

### SageMaker 노트북 인스턴스가 관리형 서비스에 적합한 이유

SageMaker 노트북 인스턴스는 AWS가 완전히 관리하는 Jupyter 노트북 환경으로, 실제로는 EC2 인스턴스를 기반으로 하지만 고객 계정이 아닌 AWS 내부 서비스 계정에서 실행됩니다. 이러한 아키텍처를 통해 AWS는 인프라 관리의 복잡성을 추상화하고, 사용자는 ML 개발에만 집중할 수 있도록 합니다.

### 아키텍처 플로우

```
고객 요청: SageMaker 노트북 생성
     ↓
AWS 서비스 계정에서 EC2 인스턴스 생성
     ↓
ENI(네트워크 인터페이스)를 통해 고객 VPC 연결
     ↓
고객은 Jupyter 인터페이스만 접근 가능
     ↓
실제 EC2 인스턴스는 AWS 계정에 숨겨진 상태
```

### Trade-offs 고려사항

**SageMaker 노트북 인스턴스 장점**:
- 완전 관리형 서비스로 인프라 관리 불필요
- 자동 패치 및 보안 업데이트
- 간편한 시작/중지 기능
- ML 라이브러리 사전 설치

**SageMaker 노트북 인스턴스 단점**:
- EC2 인스턴스에 직접 접근 불가
- EBS 볼륨 직접 관리 제한
- 커스텀 AMI 사용 불가
- 세밀한 인프라 제어 어려움

**직접 EC2 인스턴스 장점**:
- 완전한 인프라 제어
- 직접 EBS 스냅샷 관리
- 커스텀 AMI 및 설정 가능
- 비용 최적화 유연성

**직접 EC2 인스턴스 단점**:
- 직접 관리 및 유지보수 필요
- 보안 패치 및 업데이트 수동 처리
- ML 환경 구성의 복잡성

## 🔍 주요개념

### 서비스 계정 vs 고객 계정

- **AWS 서비스 계정**: AWS 내부에서 관리형 서비스 리소스를 실행하는 계정
- **고객 계정**: 사용자가 직접 관리하고 볼 수 있는 리소스가 있는 계정

### 백업 및 스냅샷 방법

**AWS CLI를 통한 스냅샷**:
```bash
# 노트북 인스턴스 중지
aws sagemaker stop-notebook-instance --notebook-instance-name my-notebook

# 스냅샷 생성 (AWS API 통해서만 가능)
aws ec2 create-snapshot --volume-id vol-xxxxxxxxx --description "SageMaker backup"
```

**대안적 백업 방법**:
- S3 동기화: `aws s3 sync /home/ec2-user/SageMaker/ s3://backup-bucket/`
- Git 리포지토리 연결
- 라이프사이클 구성을 통한 자동 백업

### 실전 적용

- **ML 프로젝트 개발**: 빠른 프로토타이핑과 실험을 위한 관리형 환경
- **교육 및 학습**: 복잡한 인프라 설정 없이 ML 학습 환경 제공
- **협업 프로젝트**: 표준화된 ML 개발 환경 공유

## 📝 관련 문제

**Question:** A Machine Learning Specialist is using an Amazon SageMaker notebook instance in a private subnet of a corporate VPC. The ML Specialist has important data stored on the Amazon SageMaker notebook instance's Amazon EBS volume, and needs to take a snapshot of that EBS volume. However, the ML Specialist cannot find the Amazon SageMaker notebook instance's EBS volume or Amazon EC2 instance within the VPC. Why is the ML Specialist not seeing the instance visible in the VPC?

**Options:**

- A) Amazon SageMaker notebook instances are based on the EC2 instances within the customer account, but they run outside of VPCs.
- B) Amazon SageMaker notebook instances are based on the Amazon ECS service within customer accounts.
- C) Amazon SageMaker notebook instances are based on EC2 instances running within AWS service accounts.
- D) Amazon SageMaker notebook instances are based on AWS ECS instances running within AWS service accounts.

**정답: C) Amazon SageMaker notebook instances are based on EC2 instances running within AWS service accounts.**

💡 추가 설명:

- **A번** - EC2 인스턴스가 고객 계정에 있다면 EC2 콘솔에서 확인할 수 있어야 하지만, 실제로는 AWS 서비스 계정에 존재
- **B번** - ECS 서비스가 아닌 EC2 인스턴스를 기반으로 하며, 여전히 서비스 계정에서 실행
- **D번** - ECS가 아닌 EC2를 기반으로 하므로 잘못된 서비스 유형을 언급