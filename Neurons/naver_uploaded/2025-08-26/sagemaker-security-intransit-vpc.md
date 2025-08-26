---

title: sagemaker-security-intransit-vpc
created: 2025-08-19
modified: 2025-08-19
tags:
- service/sagemaker
- security/encryption
- network/vpc
- data/in-transit
- industry/finance
aliases: ["sagemaker-ssl-tls", "vpc-data-security", "in-transit-encryption"]

---

# SageMaker VPC 환경에서 전송 중 데이터 보안

## 🎯 핵심 포인트

금융 서비스처럼 민감한 데이터를 다루는 경우 VPC 내에서 SageMaker 모델 훈련 시, SSL/TLS 암호화를 통해 전송 중 데이터 보안을 확보할 수 있다.

## 📝 설명

### SSL/TLS가 VPC 내 데이터 전송 보안에 적합한 이유

SSL/TLS 암호화는 애플리케이션 계층에서 작동하는 표준 보안 프로토콜로, VPC 내부 통신에서도 end-to-end 암호화를 제공합니다. SageMaker는 기본적으로 HTTPS 엔드포인트를 지원하며, 모든 API 호출과 데이터 전송에 TLS 1.2 이상을 사용합니다. 이는 네트워크 인프라 변경 없이도 즉시 적용 가능한 보안 방식입니다.

특히 금융 데이터와 같은 고민감도 정보의 경우, 데이터가 SageMaker 인스턴스와 S3 버킷, 또는 다른 데이터 소스 간을 이동할 때도 암호화된 상태를 유지해야 합니다. SSL/TLS는 이러한 요구사항을 충족하는 가장 직접적이고 효과적인 방법입니다.

### 아키텍처 플로우

```
[Data Source (S3/RDS)] 
          ↓ (SSL/TLS 암호화)
[VPC Endpoint/NAT Gateway]
          ↓ (암호화된 통신)
[SageMaker Training Instance]
          ↓ (HTTPS API 호출)
[SageMaker 관리 서비스]
          ↓ (TLS 암호화)
[모델 아티팩트 저장소]
```

### Trade-offs 고려사항

**SSL/TLS 암호화 장점**:
- 표준 프로토콜로 광범위한 지원
- AWS 서비스들과 네이티브 통합
- 네트워크 토폴로지 변경 불필요
- 인증서 관리 자동화 (AWS Certificate Manager)
- 성능 오버헤드 최소화

**SSL/TLS 암호화 단점**:
- 약간의 성능 오버헤드 (일반적으로 5% 미만)
- 인증서 갱신 및 관리 필요
- 디버깅 시 트래픽 분석 복잡성 증가

**IAM 정책 장점**:
- 세밀한 접근 권한 제어
- AWS 리소스 간 권한 관리

**IAM 정책 단점**:
- 전송 중 데이터 암호화와 직접적 관련 없음
- 저장 중 데이터(data at rest) 보안에 해당

**NAT Gateway 장점**:
- 프라이빗 서브넷에서 인터넷 접근 제공
- AWS 관리형 서비스로 고가용성

**NAT Gateway 단점**:
- 자체적으로 암호화 기능 제공 안함
- 단순 네트워크 라우팅 구성 요소
- 데이터 보안 요구사항 미충족

**VPC Peering 장점**:
- VPC 간 프라이빗 네트워크 연결
- AWS 백본 네트워크 사용

**VPC Peering 단점**:
- 네트워크 연결만 제공, 암호화 없음
- 추가적인 SSL/TLS 구성 여전히 필요
- 복잡한 네트워크 토폴로지 관리

## 🔍 주요개념

### 데이터 보안 유형 비교

- **Data at Rest**: 저장된 데이터의 암호화 (S3 SSE, EBS 암호화)
- **Data in Transit**: 전송 중 데이터의 암호화 (SSL/TLS, VPN)
- **Data in Processing**: 처리 중 데이터의 암호화 (AWS Nitro Enclaves)

### 실전 적용

- 금융 서비스의 사기 탐지 모델 훈련 시 고객 거래 데이터 보호
- 헬스케어 분야의 환자 데이터를 활용한 진단 모델 개발
- 소매업의 고객 개인정보를 포함한 추천 시스템 구축

## 📝 관련 문제

**Question:** A financial services firm is leveraging Amazon SageMaker to train a distributed deep learning model for fraud detection. The model training utilizes highly sensitive financial data, necessitating that data security is maintained while in-transit within a Virtual Private Cloud (VPC). What approach should be adopted to ensure that the data remains secure during its transit?

**Options:**

- A) Apply an IAM policy to SageMaker instances granting them permissions to encrypt data at rest, ensuring in-transit data security
- B) Enable SSL/TLS encryption for data in-transit between SageMaker and data sources within the VPC
- C) Use VPC Peering to securely transfer data between VPCs, ensuring encryption of data in-transit to SageMaker
- D) Configure a NAT Gateway to encrypt all data leaving the private subnet towards SageMaker endpoints

**정답: B) Enable SSL/TLS encryption for data in-transit between SageMaker and data sources within the VPC**

💡 추가 설명:

- **Option A** - IAM 정책은 접근 권한 제어용이며, 저장 중 데이터 암호화 권한 부여와 관련되어 전송 중 데이터 보안과는 직접적 관련이 없음
- **Option C** - VPC Peering은 네트워크 연결만 제공하며 자체적으로 암호화 기능을 제공하지 않아 추가적인 암호화 계층이 필요함
- **Option D** - NAT Gateway는 인터넷 접근을 위한 라우팅 구성 요소로 데이터 암호화 기능을 제공하지 않음