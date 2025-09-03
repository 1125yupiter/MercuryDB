---

title: vpc-vpn-ha-connectivity
created: 2025-08-19
modified: 2025-08-19
tags:
- service/vpc
- service/vpn
- constraint/high-availability
- architecture/hybrid-cloud
- networking/fault-tolerant
aliases: ["vpc-vpn", "site-to-site", "hybrid-connectivity"]

---

# VPC와 온프레미스 간 고가용성 VPN 연결

## 🎯 핵심 포인트

고가용성 애플리케이션이 Amazon VPC에서 온프레미스 데이터센터와 연결이 필요한 경우, 다중 VPN 터널과 이중화된 Customer Gateway 장비를 사용하여 장애 허용 솔루션을 구현할 수 있다.

## 📝 설명

### AWS Site-to-Site VPN이 고가용성 하이브리드 연결에 적합한 이유

AWS Site-to-Site VPN은 기본적으로 각 VPN 연결에 대해 2개의 VPN 터널을 자동 생성하여 AWS 측의 중복성을 제공합니다. 온프레미스 측에서도 여러 Customer Gateway 장비를 구성하면 완전한 이중화 환경을 구축할 수 있습니다.

각 VPN 터널은 서로 다른 AWS Availability Zone의 VPN 엔드포인트에 연결되므로, 한 쪽 AZ에 문제가 발생해도 다른 터널을 통해 연결이 유지됩니다. 온프레미스에서도 물리적으로 분리된 Customer Gateway 장비를 사용하면 하드웨어 장애나 네트워크 장애 시에도 서비스 연속성을 보장할 수 있습니다.

### 아키텍처 플로우

```
온프레미스 데이터센터          AWS VPC
┌─────────────────────┐    ┌─────────────────────┐
│                     │    │                     │
│  Customer GW #1 ────┼────┼──── VPN Tunnel #1  │
│         │           │    │          │          │
│         │           │    │          │          │
│     Internal        │    │      Private        │
│     Network         │    │      Subnets        │
│         │           │    │          │          │
│         │           │    │          │          │
│  Customer GW #2 ────┼────┼──── VPN Tunnel #2  │
│                     │    │                     │
└─────────────────────┘    └─────────────────────┘
```

### Trade-offs 고려사항

**Site-to-Site VPN 장점**:
- 빠른 구축 및 배포 가능
- 기존 인터넷 연결 활용으로 비용 효율적
- 자동 이중화 터널 제공
- BGP 라우팅으로 자동 페일오버

**Site-to-Site VPN 단점**:
- 인터넷 경유로 인한 대역폭 및 지연시간 변동성
- 처리량 제한 (최대 1.25 Gbps per tunnel)
- 암호화/복호화로 인한 CPU 오버헤드

**AWS Direct Connect 장점**:
- 예측 가능한 네트워크 성능
- 더 높은 대역폭 (최대 100 Gbps)
- 더 낮고 일관된 지연시간
- 데이터 전송 비용 절감 (대용량 시)

**AWS Direct Connect 단점**:
- 구축 시간이 길음 (몇 주에서 몇 달)
- 초기 비용이 높음
- 물리적 연결 의존성
- 단일 연결 시 SPOF 위험

## 🔍 주요개념

### 고가용성 구성 요소 비교

- **다중 VPN 터널**: AWS 측에서 자동 제공되는 이중화, 서로 다른 AZ의 VPN 엔드포인트 사용
- **이중화 Customer Gateway**: 온프레미스 측 단일 실패 지점 제거, 물리적/논리적 분리 필요
- **BGP 라우팅**: 동적 라우팅으로 장애 시 자동 경로 변경, 수초 내 페일오버 가능

### 실전 적용

- **금융 서비스**: 거래 시스템의 온프레미스 메인프레임과 AWS 분석 환경 연결
- **제조업**: 공장 IoT 데이터를 AWS로 실시간 전송하면서 제어 시스템은 온프레미스 유지  
- **헬스케어**: 환자 데이터의 규정 준수를 위해 온프레미스 저장, AWS에서 AI/ML 분석 수행

## 📝 관련 문제

**Question:** A highly available application running in an Amazon VPC requires connectivity to an on-premises data center. Which of the following options provides a highly available and fault-tolerant solution for this requirement?

**Options:**

- A) Use a VPN connection with multiple VPN tunnels and redundant customer gateway devices in the on-premises data center
- B) Use a VPN connection with multiple VPN tunnels and a single customer gateway device in the on-premises data center  
- C) Use a VPN connection with a single VPN tunnel and redundant customer gateway devices in the on-premises data center
- D) Use a VPN connection with a single VPN tunnel and a single customer gateway device in the on-premises data center

**정답: A) Use a VPN connection with multiple VPN tunnels and redundant customer gateway devices**

💡 추가 설명:

- **옵션 B** - 단일 Customer Gateway로 인해 온프레미스 측 단일 실패 지점 존재
- **옵션 C** - 단일 VPN 터널로 인해 AWS 측 VPN 엔드포인트 장애 시 연결 중단  
- **옵션 D** - 양쪽 모두 단일 실패 지점이 존재하여 고가용성 요구사항 미충족