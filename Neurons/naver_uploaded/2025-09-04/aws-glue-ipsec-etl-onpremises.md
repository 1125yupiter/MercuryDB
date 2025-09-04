---

title: aws-glue-ipsec-etl-onpremises
created: 2025-08-23
modified: 2025-08-23
tags:
- service/aws-glue
- network/ipsec-vpn
- constraint/data-localization
- security/layer3-encryption
- usecase/onpremises-etl
aliases: ["glue-ipsec", "vpn-etl", "onpremises-glue"]

---

# AWS Glue ETL with IPsec VPN for On-premises Data Integration

## 🎯 핵심 포인트

데이터 지역화 요구사항과 IPsec 암호화가 필요한 경우 AWS Glue ETL Job에서 Site-to-Site VPN을 통해 온프레미스 데이터베이스에 직접 연결하여 안전한 데이터 수집이 가능하다.

## 📝 설명

### AWS Glue가 온프레미스 ETL에 적합한 이유

AWS Glue는 JDBC 연결을 통해 온프레미스 데이터베이스에 직접 접근할 수 있는 완전 관리형 ETL 서비스입니다. Site-to-Site VPN과 결합하면 IPsec 암호화를 통한 안전한 데이터 파이프라인을 구축할 수 있습니다. Glue Job은 지정된 VPC 내에서 실행되며, VPN Gateway를 통해 온프레미스 네트워크와 안전하게 통신합니다.

### 아키텍처 플로우

```
[온프레미스 PostgreSQL] 
    ↓ (IPsec 터널)
[VPN Gateway] 
    ↓
[AWS VPC] 
    ↓
[Glue ETL Job] 
    ↓ (JDBC 연결)
[비민감 데이터 선별] 
    ↓
[Amazon S3] 
    ↓
[ML Model 훈련]
```

### Trade-offs 고려사항

**AWS Glue 장점**:
- JDBC를 통한 직접 데이터베이스 연결 지원
- VPC 내 실행으로 네트워크 보안 제어 가능
- 테이블 선별을 통한 민감 데이터 제외 용이
- 완전 관리형 서비스로 인프라 관리 불필요

**AWS Glue 단점**:
- 초기 설정 복잡성 (VPC, 서브넷, 보안 그룹 구성)
- 대용량 데이터 처리 시 비용 증가 가능성

**AWS DMS 장점**:
- 데이터베이스 마이그레이션에 특화
- 테이블 매핑을 통한 세밀한 데이터 제어

**AWS DMS 단점**:
- SSL 연결만 지원하여 IPsec 요구사항 미충족
- 일반적인 ETL 작업보다는 마이그레이션에 최적화

## 🔍 주요개념

### 네트워크 보안 계층 비교

- **IPsec (Layer 3)**: 네트워크 계층에서 모든 트래픽을 암호화하는 VPN 기술
- **SSL/TLS (Layer 7)**: 애플리케이션 계층에서 특정 프로토콜(HTTPS 등)만 암호화

### OSI 7계층 보안 기술

- **Layer 1 (물리)**: 데이터센터 물리 보안, 케이블 보안
- **Layer 2 (데이터링크)**: 802.1X, MACsec, VLAN 분리
- **Layer 3 (네트워크)**: IPsec VPN, 네트워크 방화벽 ← **IPsec 위치**
- **Layer 4 (전송)**: 포트 기반 방화벽, DDoS 방어
- **Layer 5 (세션)**: 세션 관리, 토큰 인증
- **Layer 6 (표현)**: 데이터 암호화, 디지털 서명
- **Layer 7 (응용)**: SSL/TLS, WAF, API 보안 ← **SSL/TLS 위치**

### 실전 적용

- 금융권에서 고객 데이터 처리 시 온프레미스 민감 데이터 보호
- 제조업체의 생산 데이터를 클라우드 ML 모델로 분석
- 헬스케어 분야에서 환자 정보 지역화 요구사항 충족

## 📝 관련 문제

**Question:** An online retailer wants to provide a new cloud-based product suggestion tool to its web application. Due to data localization requirements, sensitive data must remain on-premises and the product recommendation model must be trained and evaluated using only nonsensitive data. IPsec is required for data transport to the cloud. The web application is hosted on-premises and all data is stored in a PostgreSQL database. Each day, the organization needs the data securely sent to Amazon S3 for model retraining. How should a professional in machine learning achieve these requirements?

**Options:**

- A) Create an AWS Glue job to connect to the PostgreSQL DB instance. Ingest tables without sensitive data through an AWS Site-to-Site VPN connection directly into Amazon S3.
- B) Create an AWS Glue job to connect to the PostgreSQL DB instance. Ingest all data through an AWS Site-to-Site VPN connection into Amazon S3 while removing sensitive data using a PySpark job.
- C) Use AWS Database Migration Service (AWS DMS) with table mapping to select PostgreSQL tables with no sensitive data through an SSL connection. Replicate data directly into Amazon S3.
- D) Use PostgreSQL logical replication to replicate all data to PostgreSQL in Amazon EC2 through AWS Direct Connect with a VPN connection. Use AWS Glue to move data from Amazon EC2 to Amazon S3.

**정답: A) Create an AWS Glue job to connect to the PostgreSQL DB instance. Ingest tables without sensitive data through an AWS Site-to-Site VPN connection directly into Amazon S3.**

💡 추가 설명:

- **옵션 B** - 모든 데이터를 수집한 후 제거하는 방식은 민감 데이터가 일시적으로 클라우드에 전송되어 보안상 위험
- **옵션 C** - SSL 연결은 Layer 7 보안으로 IPsec(Layer 3) 요구사항을 충족하지 못함
- **옵션 D** - 모든 데이터를 EC2로 복제하면 민감 데이터도 클라우드로 이전되어 데이터 지역화 요구사항 위반

---