---

title: aws-security-groups-nacl-stateful-stateless
created: 2025-08-19
modified: 2025-08-19
tags:
- aws/security-groups
- aws/network-acl
- networking/stateful
- networking/stateless
- vpc/traffic-control
aliases: ["sg-nacl", "stateful-stateless", "vpc-security"]

---

# AWS Security Groups와 Network ACL의 Stateful/Stateless 특성

## 🎯 핵심 포인트

VPC에서 네트워크 보안을 설정하는 경우 Security Groups는 stateful로 응답 트래픽을 자동 허용하고, Network ACL은 stateless로 인바운드/아웃바운드 규칙을 각각 명시해야 한다.

## 📝 설명

### AWS VPC 보안 계층이 트래픽 제어에 적합한 이유

AWS VPC는 두 가지 주요 보안 메커니즘을 제공한다. Security Groups는 인스턴스 레벨에서 가상 방화벽 역할을 하며, Network ACL은 서브넷 레벨에서 추가 보안 계층을 제공한다. 두 메커니즘의 가장 중요한 차이점은 상태 관리 방식이다.

Security Groups는 **stateful**하게 작동하여 연결 상태를 추적한다. 한 번 허용된 인바운드 트래픽에 대한 응답은 아웃바운드 규칙과 관계없이 자동으로 허용된다. 이는 connection tracking을 통해 구현되며, 관리 복잡성을 크게 줄여준다.

Network ACL은 **stateless**하게 작동하여 각 패킷을 독립적으로 평가한다. 인바운드와 아웃바운드 트래픽에 대해 별도의 규칙이 필요하며, ephemeral port 범위도 고려해야 한다.

### 아키텍처 플로우

```
Internet Gateway
       ↓
   Network ACL (Subnet Level - Stateless)
       ↓
Security Group (Instance Level - Stateful)
       ↓
   EC2 Instance

Traffic Flow:
1. 인바운드: Internet → NACL → Security Group → Instance
2. 아웃바운드: Instance → Security Group → NACL → Internet

Stateful vs Stateless:
- Security Group: 인바운드 허용 → 응답 자동 허용
- Network ACL: 인바운드/아웃바운드 각각 명시적 허용 필요
```

### Trade-offs 고려사항

**Security Groups 장점**:
- 상태 추적으로 관리 단순화
- 응답 트래픽 자동 허용
- 다른 Security Group 참조 가능
- 인스턴스별 세밀한 제어

**Security Groups 단점**:
- Allow 규칙만 지원 (Deny 불가)
- 특정 IP 차단 불가
- 서브넷 레벨 제어 불가

**Network ACL 장점**:
- Allow/Deny 규칙 모두 지원
- 서브넷 레벨 광범위 제어
- 특정 IP 주소 차단 가능
- 규칙 우선순위 제어

**Network ACL 단점**:
- 상태 비추적으로 관리 복잡
- Ephemeral port 범위 고려 필요
- 인바운드/아웃바운드 별도 설정

## 🔍 주요개념

### Stateful vs Stateless 비교

- **Stateful (Security Groups)**: 연결 상태를 기억하여 응답 트래픽을 자동 허용. 예: 웹서버 포트 80 인바운드 허용 시 클라이언트 응답도 자동 허용
- **Stateless (Network ACL)**: 각 패킷을 독립적으로 평가. 예: 포트 80 인바운드 허용해도 클라이언트로의 응답을 위한 ephemeral port 아웃바운드를 별도 허용 필요

### 실전 적용

- **웹 애플리케이션**: Security Group으로 HTTP/HTTPS 허용, NACL로 의심스러운 IP 대역 차단
- **데이터베이스 서버**: Security Group으로 애플리케이션 서버만 접근 허용, NACL로 서브넷 레벨 보안 강화
- **SSH 접근 제어**: Security Group으로 관리자 IP만 허용, NACL로 SSH 포트 전체 서브넷 차단

## 📝 관련 문제

**Question:** A company is deploying a web application in AWS VPC and needs to implement network security. The application requires HTTP/HTTPS access from the internet and database connectivity within the subnet. What is the key difference between Security Groups and Network ACLs regarding traffic handling?

**Options:**

- A) Network ACLs provide stateful filtering at subnet level, while Security Groups provide stateless filtering at instance level
- B) Network ACLs provide stateless filtering at subnet level, while Security Groups provide stateful filtering at instance level  
- C) Both Network ACLs and Security Groups provide stateful filtering but at different levels
- D) Both Network ACLs and Security Groups provide stateless filtering but Security Groups are more restrictive
- E) Security Groups operate at subnet level with stateful filtering, while Network ACLs operate at instance level

**정답: B) Network ACLs provide stateless filtering at subnet level, while Security Groups provide stateful filtering at instance level**

💡 추가 설명:

- **Option A** - Stateful/Stateless 특성이 완전히 반대로 설명됨
- **Option C** - Network ACL은 stateless이므로 부정확
- **Option D** - Security Groups는 stateful이므로 부정확  
- **Option E** - 작동 레벨이 반대로 설명됨