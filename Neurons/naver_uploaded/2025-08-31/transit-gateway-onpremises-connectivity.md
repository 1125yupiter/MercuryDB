---

title: transit-gateway-onpremises-connectivity
created: 2025-08-21
modified: 2025-08-21
tags:
- service/transit-gateway
- connectivity/hybrid-cloud
- architecture/hub-spoke
- network/onpremises
- aws/vpc-connectivity
aliases: ["TGW", "transit-gateway", "vpc-connectivity"]

---

# AWS Transit Gateway를 활용한 온프레미스-VPC 연결성 아키텍처

## 🎯 핵심 포인트

복잡한 하이브리드 네트워크 환경에서 온프레미스와 여러 VPC를 연결해야 하는 경우, AWS Transit Gateway를 중앙 허브로 활용하여 네트워크 관리를 단순화하고 확장 가능한 아키텍처를 구축할 수 있다.

## 📝 설명

### Transit Gateway가 하이브리드 클라우드 연결에 적합한 이유

AWS Transit Gateway는 네트워크 허브 역할을 하는 완전 관리형 서비스로, Hub-and-Spoke 모델을 통해 복잡한 메시 네트워크 구조를 단순화합니다. 온프레미스 데이터센터와 여러 VPC 간의 연결을 중앙집중식으로 관리하여 네트워크 복잡성을 크게 줄이고 운영 효율성을 향상시킵니다.

**주요 특징:**
- AWS 클라우드 내에서만 존재하는 관리형 서비스
- VPC Attachment를 통한 직접적인 VPC 연결
- 세밀한 라우팅 제어 및 보안 정책 적용
- 최대 5,000개 VPC 연결 지원

### 아키텍처 플로우

```
온프레미스 환경                    AWS 클라우드
┌─────────────────┐              ┌──────────────────────┐
│  본사 데이터센터    │              │                      │
│  지사A           │   VPN/DX     │   Transit Gateway    │
│  지사B           │  ────────→   │      (중앙 허브)       │
│  지사C           │              │                      │
└─────────────────┘              │  ┌─────┬─────┬─────┐ │
                                 │  │VPC-A│VPC-B│VPC-C│ │
                                 │  │ 개발 │ 운영 │ 공유 │ │
                                 │  └─────┴─────┴─────┘ │
                                 └──────────────────────┘

연결 방식:
온프레미스 → VPN/Direct Connect → Transit Gateway → VPC Attachment → VPC
```

### Trade-offs 고려사항

**Transit Gateway 장점:**
- 중앙집중식 네트워크 관리로 운영 복잡성 감소
- 확장성이 뛰어나며 새로운 VPC 추가가 용이
- 세밀한 라우팅 제어 및 보안 정책 적용 가능
- AWS 완전 관리형 서비스로 인프라 관리 부담 최소화

**Transit Gateway 단점:**
- 비용 부담 (시간당 요금 + 데이터 처리 요금 + 연결 요금)
- 단일 장애점(SPOF) 위험
- 크로스 AZ 트래픽 시 추가 비용 발생
- 리전별 서비스로 글로벌 연결 시 복잡성 증가

**VPC Peering 장점:**
- 저비용으로 일대일 직접 연결 가능
- 높은 성능과 낮은 지연시간

**VPC Peering 단점:**
- 다중 VPC 환경에서 관리 복잡성 급격히 증가
- 전이적 라우팅 불가능 (A-B, B-C 연결 시 A-C 직접 통신 불가)

## 🔍 주요개념

### 핵심 개념 비교

- **VPC Attachment**: Transit Gateway와 VPC 간의 네트워크 연결로, 모든 종류의 네트워크 트래픽이 양방향으로 흐를 수 있는 일반적인 네트워크 연결
- **VPC Endpoint**: VPC에서 특정 AWS 서비스(S3, DynamoDB 등)로의 전용 연결로, API 호출만을 위한 특수 목적 연결점

### 실전 적용

- **다중 환경 관리**: 개발/스테이징/운영 VPC를 분리하면서도 온프레미스에서 통합 접근
- **글로벌 확장**: 여러 리전의 Transit Gateway를 Peering으로 연결하여 글로벌 네트워크 구축
- **보안 구역화**: 라우트 테이블을 활용하여 네트워크 세그멘테이션 및 접근 제어 구현

## 📝 관련 문제

**Question:** Your company has multiple VPCs across different environments (development, staging, production) and needs to connect them with on-premises data centers located in various geographical locations. Which AWS service provides the most effective centralized connectivity solution?

**Options:**

- A) AWS Direct Connect provides a dedicated, high-speed, low-latency connection between on-premises data centers and Amazon VPC
- B) AWS Site-to-Site VPN provides a dedicated, high-speed, low-latency connection between on-premises data centers and Amazon VPC
- C) Amazon VPC does not support connectivity with on-premises data centers
- D) AWS Transit Gateway provides a dedicated, high-speed, low-latency connection between on-premises data centers and Amazon VPC
- E) AWS PrivateLink provides the best solution for connecting multiple VPCs with on-premises networks

**정답: D) AWS Transit Gateway**

💡 추가 설명:

- **Direct Connect** - 물리적 전용선 연결 서비스이지만 VPC가 제공하는 기능이 아닌 독립적인 AWS 서비스
- **Site-to-Site VPN** - 인터넷 기반 연결로 "dedicated" 연결이 아니며 대역폭과 지연시간 제한 존재
- **연결 지원 안 함** - 명백히 잘못된 선택지로 VPC는 다양한 연결 옵션 제공
- **PrivateLink** - AWS 서비스 간 연결에 특화되어 있으며 온프레미스 연결에는 직접적으로 사용되지 않음