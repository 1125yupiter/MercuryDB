---

title: sagemaker-vpc-security-privatelink
created: 2025-08-17
modified: 2025-08-17
tags:
- service/sagemaker
- security/vpc-endpoint
- network/private-subnet
- constraint/no-internet
- method/privatelink
aliases: ["sagemaker-private", "vpc-endpoint-sagemaker", "private-ml"]

---

# SageMaker VPC 보안 - Private 환경에서 ML 노트북 운영

## 🎯 핵심 포인트

데이터 보안이 중요한 경우 private subnet에서 VPC 엔드포인트를 통해 SageMaker 노트북을 안전하게 운영할 수 있다.

## 📝 설명

### SageMaker가 보안이 중요한 ML 환경에 적합한 이유

SageMaker는 AWS PrivateLink를 통한 VPC 엔드포인트를 지원하여 모든 트래픽을 AWS 내부 네트워크로 제한할 수 있습니다. 이를 통해 인터넷 노출 없이도 필요한 AWS 서비스에 접근 가능하며, 악성 코드나 외부 침입으로부터 훈련 데이터를 보호할 수 있습니다.

### 아키텍처 플로우

```
Private Subnet (SageMaker 노트북)
    ↓ (AWS PrivateLink)
VPC Endpoint for SageMaker API
    ↓ (내부 네트워크)
SageMaker Service
    ↓
VPC Endpoint for S3
    ↓ (내부 네트워크)
S3 Bucket (훈련 데이터)
```

### Trade-offs 고려사항

**VPC 엔드포인트 + Private Subnet 장점**:
- 완전한 네트워크 격리로 최고 수준의 보안
- AWS PrivateLink를 통한 안전한 서비스 접근
- 외부 악성 코드 침입 차단
- 데이터 유출 위험 최소화

**VPC 엔드포인트 + Private Subnet 단점**:
- 초기 설정 복잡성
- VPC 엔드포인트 비용 추가
- 외부 패키지 설치 시 추가 구성 필요

**Bastion Host 장점**:
- 관리자 접근 경로 제공
- 상대적으로 간단한 설정

**Bastion Host 단점**:
- 여전히 인터넷 게이트웨이 필요
- 추가 보안 관리 포인트 생성
- 관리 복잡성 증가

**VPC Peering 장점**:
- VPC 간 연결 제공

**VPC Peering 단점**:
- SageMaker API 접근 문제 미해결
- 복잡한 라우팅 설정

**NACL 규칙 장점**:
- 네트워크 레벨 접근 제어

**NACL 규칙 단점**:
- SageMaker API 접근 차단으로 서비스 작동 불가
- 필요한 기능까지 차단

## 🔍 주요개념

### 핵심 개념 비교

- **VPC 엔드포인트**: AWS 서비스에 대한 프라이빗 연결을 제공하는 가상 디바이스
- **AWS PrivateLink**: AWS 서비스 간 프라이빗 연결을 가능하게 하는 기술
- **Private Subnet**: 인터넷 게이트웨이로의 직접 라우트가 없는 서브넷
- **Interface Endpoint**: ENI 기반으로 특정 AWS 서비스에 접근하는 VPC 엔드포인트

### 실전 적용

- 금융 기관의 고객 데이터 분석 시 규제 준수를 위한 완전 격리 환경
- 의료 기관의 환자 데이터 분석 시 HIPAA 준수를 위한 보안 환경
- 제조업체의 영업 기밀 데이터 분석 시 지적재산 보호 환경

## 📝 관련 문제

**Question:** A company is preparing a lab playground of Amazon SageMaker notebooks for its Data Science team. A new security policy requires that the training data, stored in an Amazon S3 bucket, is confined within the AWS network only and must not be exposed to the public Internet. The company currently has Internet-enabled notebook instances that could possibly run malicious code that may compromise data privacy. How can the company achieve this?

**Options:**

- A) Host the SageMaker notebooks in a private subnet of a VPC and associate a VPC endpoint for Amazon SageMaker notebook and Amazon S3
- B) Host the SageMaker notebooks in a private subnet of a VPC. Launch a bastion host in the public subnet of the company VPC
- C) Host the SageMaker notebooks in a private subnet of a VPC. Connect to the VPC that hosts the Amazon SageMaker instances through VPC peering
- D) Host the SageMaker notebooks in a private subnet of a VPC. Create an outbound Network Access Control List (NACL) rule that blocks access from the public Internet

**정답: A) Host the SageMaker notebooks in a private subnet of a VPC and associate a VPC endpoint for Amazon SageMaker notebook and Amazon S3**

💡 추가 설명:

- **옵션 B (Bastion Host)** - 여전히 인터넷 게이트웨이가 필요하고 관리 복잡성이 증가하며 완전한 격리를 제공하지 못함
- **옵션 C (VPC Peering)** - VPC 간 연결만 해결하고 SageMaker API 접근 문제는 해결하지 못하여 서비스 기능 제한
- **옵션 D (NACL 규칙)** - SageMaker API 접근을 차단하여 노트북 인스턴스가 정상 작동할 수 없음