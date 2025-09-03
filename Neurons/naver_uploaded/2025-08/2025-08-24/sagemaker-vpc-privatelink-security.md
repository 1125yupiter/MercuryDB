---

title: sagemaker-vpc-privatelink-security
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker
- security/vpc-endpoints
- connectivity/privatelink
- constraint/no-internet
- industry/financial
aliases: ["sagemaker-private", "vpc-sagemaker", "privatelink-ml"]

---

# SageMaker VPC 보안 - PrivateLink를 통한 비공개 연결

## 🎯 핵심 포인트

민감한 데이터를 다루는 환경에서 인터넷 접근을 차단해야 하는 경우, VPC Endpoints와 PrivateLink를 통해 SageMaker 노트북에 안전하게 접근할 수 있다.

## 📝 설명

### PrivateLink가 금융 서비스 보안 요구사항에 적합한 이유

AWS PrivateLink는 AWS 서비스에 대한 완전한 비공개 연결을 제공하는 네트워킹 서비스입니다. 모든 트래픽이 AWS 내부 백본 네트워크를 통해 전송되어 공용 인터넷을 완전히 우회합니다. 금융 서비스처럼 높은 보안이 요구되는 환경에서는 데이터 유출 위험을 최소화하면서도 필요한 AWS 서비스에 접근할 수 있는 핵심 솔루션입니다.

### 아키텍처 플로우

```
[Private Subnet] → [VPC Endpoint] → [PrivateLink] → [SageMaker Service]
        ↓               ↓              ↓                    ↓
[SageMaker Notebook] [Interface Endpoint] [AWS Backbone] [SageMaker API]
        ↓               ↓              ↓                    ↓
[Security Groups] → [Route Tables] → [Private DNS] → [Service Access]
```

### Trade-offs 고려사항

**PrivateLink + VPC Endpoints 장점**:
- 완전한 인터넷 격리로 최고 수준의 보안 제공
- AWS 내부 네트워크 사용으로 안정적인 성능
- 세밀한 접근 제어 및 모니터링 가능
- 컴플라이언스 요구사항 충족

**PrivateLink + VPC Endpoints 단점**:
- 추가적인 VPC Endpoint 비용 발생
- 초기 설정의 복잡성
- 외부 라이브러리 설치 시 제약사항

**NAT Gateway 장점**:
- 외부 패키지 설치 용이
- 설정이 상대적으로 간단

**NAT Gateway 단점**:
- 아웃바운드 인터넷 연결로 보안 정책 위배
- 데이터 유출 위험 존재

## 🔍 주요개념

### 네트워크 연결 방식 비교

- **PrivateLink**: AWS 서비스 간 완전 비공개 연결, 인터넷 우회
- **VPC Peering**: VPC 간 네트워크 연결, SageMaker 직접 접근 불가
- **NAT Gateway**: 프라이빗 서브넷의 아웃바운드 인터넷 연결
- **Client VPN**: 클라이언트에서 AWS로의 VPN 연결

### 실전 적용

- 금융기관의 고객 신용평가 모델 개발 시 고객 데이터 완전 격리
- 의료기관의 환자 데이터 분석 시 HIPAA 컴플라이언스 준수
- 정부기관의 기밀 데이터 처리 시 보안 등급별 네트워크 분리

## 📝 관련 문제

**Question:** A financial services firm is leveraging Amazon SageMaker to develop machine learning models that predict market trends. Due to the sensitive nature of their data, the firm's policy prohibits direct internet access from their virtual private cloud (VPC) to ensure the security of their data. They require the ability to use SageMaker notebook instances for model development without exposing these instances to the internet. What approach should the firm take to securely utilize SageMaker notebooks within their VPC in compliance with their security policy?

**Options:**

- A) Disable internet access for SageMaker, create NAT Gateway, update route tables, and link to SageMaker notebooks
- B) Disable internet access for SageMaker, configure VPC peering, update security groups, and access SageMaker notebooks through peered VPC
- C) Disable internet access for SageMaker, establish VPC endpoints, update security groups, and access SageMaker notebooks through PrivateLink
- D) Disable internet access for SageMaker, establish VPC endpoints, update security groups, and access SageMaker notebooks through AWS Client VPN

**정답: C) Disable internet access for SageMaker, establish VPC endpoints, update security groups, and access SageMaker notebooks through PrivateLink**

💡 추가 설명:

- **NAT Gateway (A)** - 아웃바운드 인터넷 연결을 허용하여 "인터넷 접근 금지" 정책에 위배
- **VPC Peering (B)** - VPC 간 연결용으로 설계되어 SageMaker 노트북 직접 접근에 부적합
- **AWS Client VPN (D)** - 클라이언트 디바이스용 VPN으로 여전히 공용 인터넷을 경유