---

title: vpc-sagemaker-endpoint-security
created: 2025-08-27
modified: 2025-08-27
tags:
- service/sagemaker
- service/vpc
- security/endpoint
- network/private
- aws/iam
aliases: ["vpc-sagemaker", "endpoint-security", "vpc-security"]

---

# VPC와 SageMaker 서비스 API 보안 구성

## 🎯 핵심 포인트

VPC 환경에서 특정 인스턴스와 IAM 사용자의 SageMaker API 접근을 제어하고 싶은 경우 VPC Endpoint Policy와 IAM Policy를 조합하여 안전한 트래픽 제어를 할 수 있다.

## 📝 설명

### AWS 네트워크 보안 아키텍처의 이해

AWS에서 ML 워크로드를 안전하게 구성하기 위해서는 여러 계층의 보안이 필요합니다. 특히 VPC 환경에서 SageMaker 같은 관리형 서비스에 접근할 때는 네트워크 격리와 접근 권한 제어가 핵심입니다.

### VPC의 물리적 구조와 보안 개념

```
AWS 데이터센터 (서울 리전)
┌─────────────────────────────────────┐
│        물리적 AWS 인프라             │
│  ┌───────────┐  ┌──────────────┐   │
│  │    VPC    │  │  SageMaker   │   │
│  │ (고객환경) │──│   Service    │   │
│  │           │  │              │   │
│  │ Private   │  │   S3 등      │   │
│  │ Subnet    │  │   기타서비스  │   │
│  └───────────┘  └──────────────┘   │
│                                     │
│  VPC Endpoint = 내부 전용 통로      │
└─────────────────────────────────────┘
```

### 네트워크 계층별 보안 제어

**1. Region/AZ 레벨**
- 지리적 격리 및 가용성 확보

**2. VPC 레벨**  
- 논리적 네트워크 격리 (가상의 전용 데이터센터)

**3. Subnet 레벨**
- Public/Private 서브넷으로 인터넷 접근 제어
- Network ACL로 서브넷 간 트래픽 제어

**4. Instance 레벨**
- Security Group으로 포트/프로토콜 기반 방화벽

**5. Application 레벨**
- IAM으로 서비스 접근 권한 제어
- VPC Endpoint Policy로 엔드포인트 사용 권한 제어

## 🔍 주요 개념

### VPC Endpoint vs Internet Gateway

- **VPC Endpoint**: AWS 서비스로의 전용 통로 (프라이빗 통신)
- **Internet Gateway**: 인터넷으로의 통로 (퍼블릭 통신)

### 보안 정책의 이중 구조

- **IAM Policy**: "이 사용자가 뭘 할 수 있는가" (권한 관리)
- **VPC Endpoint Policy**: "이 통로를 누가 쓸 수 있는가" (접근 제어)

### 실전 적용

- **개발 환경**: Private Subnet + VPC Endpoint로 안전한 개발환경 구성
- **운영 환경**: 다계층 보안으로 프로덕션 워크로드 보호  
- **규제 준수**: 금융/의료 등 민감 데이터 처리 시 네트워크 격리

## 📝 관련 문제

**Question:** A machine learning (ML) specialist wants to secure calls to the Amazon SageMaker Service API. The specialist has configured Amazon VPC with a VPC interface endpoint for the Amazon SageMaker Service API and is attempting to secure traffic from specific sets of instances and IAM users. The VPC is configured with a single public subnet. Which combination of steps should the ML specialist take to secure the traffic? (Choose two.)

**Options:**

- A) Add a VPC endpoint policy to allow access to the IAM users
- B) Modify the users' IAM policy to allow access to Amazon SageMaker Service API calls only
- C) Modify the security group on the endpoint network interface to restrict access to the instances
- D) Modify the ACL on the endpoint network interface to restrict access to the instances
- E) Add a SageMaker Runtime VPC endpoint interface to the VPC

**정답: A, B) VPC Endpoint Policy + IAM Policy**

💡 추가 설명:

- **C (Security Group 수정)** - 네트워크 레벨 제어이지만 IAM 사용자별 세밀한 제어에는 부적합
- **D (Network ACL 수정)** - 서브넷 레벨 제어로 사용자별 제어보다는 광범위한 네트워크 제어에 적합
- **E (SageMaker Runtime 추가)** - 모델 추론용 엔드포인트로 Service API 보안과는 별개

### 보안 제어 메커니즘 비교

**VPC Endpoint Policy**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::account:user/specific-user"
      },
      "Action": "sagemaker:*",
      "Resource": "*"
    }
  ]
}
```

**IAM Policy**
```json
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sagemaker:CreateTrainingJob",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:RequestedRegion": "us-east-1"
        }
      }
    }
  ]
}
```

이 두 정책의 조합으로 "누가(VPC Endpoint Policy) + 무엇을(IAM Policy)" 세밀하게 제어할 수 있습니다.

---