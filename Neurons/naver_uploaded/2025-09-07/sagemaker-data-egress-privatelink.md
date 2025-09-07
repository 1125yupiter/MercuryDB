---

title: sagemaker-data-egress-privatelink
created: 2025-08-24 
modified: 2025-08-24 
tags:
- service/sagemaker
- security/data-egress
- network/privatelink
- constraint/financial-data
- deployment/network-isolation
aliases: ["SageMaker Security", "Data Egress Control", "PrivateLink"]

---

# SageMaker 데이터 유출 방지를 위한 AWS PrivateLink 활용

## 🎯 핵심 포인트

민감한 금융 데이터를 다루는 SageMaker 환경에서 데이터 유출을 방지하고자 하는 경우, VPC Interface Endpoint(PrivateLink), 네트워크 격리, IP 제한을 조합하여 완전한 프라이빗 연결을 구축할 수 있다.

## 📝 설명

### SageMaker가 데이터 유출 방지에 적합한 이유

AWS SageMaker는 머신러닝 워크플로우 전반에 걸쳐 다층 보안 제어를 제공합니다. 특히 금융 서비스와 같이 민감한 데이터를 다루는 환경에서는 데이터가 AWS 네트워크 외부로 유출되는 것을 원천 차단하는 것이 핵심입니다.

**AWS PrivateLink**는 VPC와 AWS 서비스 간 프라이빗 연결을 제공하는 핵심 기술로, 모든 트래픽이 AWS 내부 네트워크에서만 처리되어 인터넷을 거치지 않습니다. Interface VPC Endpoint를 통해 SageMaker 서비스를 VPC 내부로 가져와 ENI(Elastic Network Interface)와 프라이빗 IP를 사용한 통신이 가능합니다.

### 아키텍처 플로우

```
온프레미스 데이터센터
        ↓ (AWS Direct Connect - 전용회선)
AWS VPC (Private Subnet)
        ↓ (Interface VPC Endpoint - PrivateLink)
SageMaker Training Job/Model
        ↓ (Network Isolation 활성화)
완전 격리된 컨테이너 환경 (인터넷 접근 차단)
```

**온프레미스 연결 플로우:**
```
회사 네트워크 → Direct Connect → AWS 백본 네트워크 → VPC → PrivateLink → SageMaker
(전 구간 프라이빗, 인터넷 우회)
```

### Trade-offs 고려사항

**AWS PrivateLink + Network Isolation 장점**:
- 완전한 데이터 유출 방지 (인터넷 접근 원천 차단)
- AWS 내부 네트워크에서만 통신 (보안성 최대화)
- 온프레미스와 Direct Connect로 프라이빗 연결 가능
- VPC 보안 그룹으로 세밀한 접근 제어
- 규정 준수 요구사항 충족 (HIPAA, PCI-DSS 등)

**AWS PrivateLink + Network Isolation 단점**:
- 추가 비용 발생 (시간당 $0.01 + 데이터 처리비 GB당 $0.01)
- 인터넷 기반 패키지 설치 불가 (사전 준비 필요)
- 설정 복잡성 증가

**암호화만 사용하는 경우 장점**:
- 구현 단순함
- 추가 네트워크 비용 없음

**암호화만 사용하는 경우 단점**:
- 데이터 유출 자체는 방지하지 못함 (암호화된 데이터도 전송 가능)
- 인터넷을 통한 데이터 노출 위험 존재
- 금융 서비스 보안 요구사항 미충족

## 🔍 주요개념

### 데이터 유출 방지 vs 데이터 보호

- **데이터 유출 방지**: 데이터가 승인되지 않은 네트워크로 전송되는 것 자체를 차단
- **데이터 보호**: 전송되는 데이터를 암호화하여 읽기 어렵게 만듦 (전송은 허용)

### PrivateLink 연결 방식

- **Interface Endpoint**: ENI를 통해 AWS 서비스를 VPC 내부로 가져옴
- **Gateway Endpoint**: 라우팅 테이블을 통한 연결 (S3, DynamoDB만 지원)
- **Network Isolation**: 컨테이너의 모든 외부 네트워크 접근 차단

### 실전 적용

- **금융 기관**: 고객 거래 데이터 분석 시 규제 준수를 위한 완전 격리 환경
- **의료 기관**: 환자 데이터 처리 시 HIPAA 규정 준수를 위한 프라이빗 연결
- **정부 기관**: 민감 정보 처리 시 보안 등급별 네트워크 분리

## 📝 관련 문제

**Question:** A financial services firm wants to make Amazon SageMaker its primary data science environment. On sensitive financial data, the company's data scientists run machine learning (ML) models. The organization is concerned about data egress and desires the services of a machine learning engineer to safeguard the environment. Which methods does the machine learning engineer have at his disposal to manage data egress from SageMaker? (Select three.)

**Options:**
- A) Connect to SageMaker by using a VPC interface endpoint powered by AWS PrivateLink
- B) Use SCPs to restrict access to SageMaker
- C) Disable root access on the SageMaker notebook instances
- D) Enable network isolation for training jobs and models
- E) Restrict notebook presigned URLs to specific IPs used by the company
- F) Protect data with encryption at rest and in transit. Use AWS Key Management Service (AWS KMS) to manage encryption keys

**정답: A, D, E) PrivateLink + Network Isolation + IP 제한**

💡 추가 설명:

- **A) VPC Interface Endpoint (PrivateLink)** - 인터넷을 거치지 않고 AWS 내부 네트워크로만 SageMaker 접근, 데이터 유출 경로 원천 차단
- **D) Network Isolation** - 훈련 작업과 모델 컨테이너의 모든 인터넷 접근 완전 차단, 가장 강력한 유출 방지 수단
- **E) IP 제한** - 승인된 회사 IP에서만 노트북 접근 가능, 무단 접근을 통한 데이터 유출 방지
- **B) SCP** - 서비스 접근 권한 제어이지 데이터 유출 방지가 아님
- **C) Root 접근 비활성화** - 권한 관리 차원이며 네트워크 유출과는 무관
- **F) 암호화** - 데이터 보호는 되지만 유출 자체를 방지하지 못함, 암호화된 데이터도 여전히 외부 전송 가능