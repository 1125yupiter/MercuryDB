---
title: AWS-VPN-HighAvailability-FaultTolerant-OnPremises.md
created: 2025-08-15
modified: 2025-08-15
tags:
- service/site-to-site-vpn
- architecture/high-availability
- network/hybrid-connectivity
- concept/fault-tolerance
- infrastructure/redundancy
aliases: ["AWS VPN HA", "Site-to-Site VPN", "VPN Redundancy", "Hybrid Network"]
---

# AWS Site-to-Site VPN 고가용성 구성 - 온프레미스와 VPC 간 내결함성 연결

## 🎯 핵심 포인트
온프레미스 데이터센터와 AWS VPC 간 고가용성 연결이 필요한 경우 Multiple VPN Tunnels와 Redundant Customer Gateways를 함께 구성해서, 단일 장애점을 제거하고 내결함성을 확보할 수 있다.

## 📝 설명

### AWS Site-to-Site VPN이 고가용성 하이브리드 연결에 적합한 이유

AWS Site-to-Site VPN은 온프레미스와 클라우드 간 안전하고 신뢰할 수 있는 연결을 제공하는 관리형 서비스입니다. 기본적으로 각 VPN 연결당 2개의 터널을 서로 다른 AWS Availability Zone에 생성하여 99.95% 가용성 SLA를 제공합니다.

**핵심 구성 요소:**
- **VPN Tunnel**: 암호화된 논리적 연결 통로 (IPSec 프로토콜 사용)
- **Customer Gateway**: 온프레미스 측 VPN 연결 진입점
- **Virtual Private Gateway**: AWS VPC 측 VPN 연결 진입점

### 아키텍처 플로우
```
온프레미스 데이터센터                    AWS VPC
┌─────────────────────┐                ┌─────────────────────┐
│     내부 네트워크    │                │   Private Subnets   │
│         │           │                │                     │
│   [Customer         │   Tunnel 1     │                     │
│    Gateway A]       ├════════════════┤                     │
│         │           │                │   [Virtual Private  │
│   [Customer         │   Tunnel 2     │    Gateway]         │
│    Gateway B]       ├════════════════┤                     │
│                     │                │                     │
└─────────────────────┘                └─────────────────────┘
        │                                        │
        ▼                                        ▼
   장애 시 자동 failover              99.95% 가용성 보장
```

### Trade-offs 고려사항

**Multiple VPN Tunnels + Redundant Customer Gateways 장점**:
- 완전한 단일 장애점 제거 (문과 통로 모두 이중화)
- 자동 failover 및 load balancing 지원
- 네트워크 경로 다양성으로 성능 최적화 가능
- 각 구성요소 독립적 보안 관리

**Multiple VPN Tunnels + Redundant Customer Gateways 단점**:
- 초기 구축 비용 증가 (장비 및 회선 이중화)
- 관리 복잡성 증가 (여러 게이트웨이 모니터링 필요)
- 대역폭 비용 증가 (중복 회선 유지)

**Single VPN Tunnel + Single Customer Gateway 장점**:
- 낮은 초기 비용과 단순한 관리
- 기본적인 연결 요구사항 충족

**Single VPN Tunnel + Single Customer Gateway 단점**:
- 단일 장애점 존재로 가용성 보장 불가
- 장애 시 수동 복구 필요
- 고가용성 애플리케이션 요구사항 미충족

## 🔍 주요개념

### Fault-Tolerance vs Redundancy 비교
- **Fault-Tolerance (내결함성)**: 시스템 일부가 고장나더라도 전체 시스템이 계속 정상 작동하는 능력
- **Redundancy (중복성)**: 동일한 기능을 수행하는 여러 구성요소를 배치하여 장애 대비책을 마련하는 설계 방식

### 실전 적용
- **금융 서비스**: 실시간 거래 시스템의 온프레미스-클라우드 간 연결
- **제조업**: 공장 IoT 데이터의 실시간 클라우드 전송 및 분석
- **헬스케어**: 의료 데이터의 안전한 백업 및 재해 복구 시스템

## 📝 관련 문제

**Question:** You have a highly available application running in an Amazon VPC that requires connectivity to an on-premises data center. Which of the following options provides a highly available and fault-tolerant solution for this requirement?

**Options:**
- A) Use a VPN connection with a single VPN tunnel and a single customer gateway device in the on-premises data center
- B) Use a VPN connection with a single VPN tunnel and redundant customer gateway devices in the on-premises data center  
- C) Use a VPN connection with multiple VPN tunnels and a single customer gateway device in the on-premises data center
- D) Use a VPN connection with multiple VPN tunnels and redundant customer gateway devices in the on-premises data center
- E) Use AWS Direct Connect with a single connection

**정답: D) Use a VPN connection with multiple VPN tunnels and redundant customer gateway devices in the on-premises data center**

💡 추가 설명:
- **옵션 A** - 터널과 게이트웨이 모두 단일 장애점으로 고가용성 보장 불가
- **옵션 B** - 단일 터널이 장애점이 되어 게이트웨이 중복화의 효과 제한
- **옵션 C** - 단일 게이트웨이가 장애점이 되어 터널 중복화의 효과 제한  
- **옵션 E** - Direct Connect 단일 연결은 물리적 회선 장애 시 연결 중단 위험