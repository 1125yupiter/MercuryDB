---

title: network-security-osi-7layers-ipsec-ssl
created: 2025-08-25
modified: 2025-08-25
tags:
- security/network-security
- protocol/ipsec
- protocol/ssl-tls
- architecture/osi-model
- concept/network-layers
aliases: ["network security guide", "ipsec vs ssl", "7layer security", "osi security", "network protection"]

---

# 네트워크 보안 7계층 모델과 IPsec vs SSL/TLS 비교

## 🎯 핵심 포인트

네트워크 전체 보안이 필요한 경우 3계층에서 IPsec을 사용하고, 특정 애플리케이션 보안이 필요한 경우 7계층에서 SSL/TLS를 사용할 수 있다.

## 📝 설명

### OSI 7계층이 네트워크 보안에 적합한 이유

네트워크 보안을 체계적으로 구축하려면 각 계층별로 서로 다른 보안 위협과 대응 방법을 이해해야 합니다. 특히 클라우드 환경에서는 계층별 보안 아키텍처 설계가 핵심입니다. IPsec은 네트워크 계층(Layer 3)에서 전체 네트워크 트래픽을 보호하고, SSL/TLS는 애플리케이션 계층(Layer 7)에서 특정 서비스의 통신을 보호합니다.

### 아키텍처 플로우

```
Layer 7 (Application)  → SSL/TLS, WAF, API Security
Layer 6 (Presentation) → Data Encryption, Digital Signature
Layer 5 (Session)      → Session Management, Token Auth
Layer 4 (Transport)    → Port-based Firewall, Load Balancer
Layer 3 (Network)      → IPsec VPN, Network Firewall ⭐
Layer 2 (Data Link)    → 802.1X, MACsec, VLAN
Layer 1 (Physical)     → Physical Security, Cable Encryption
```

### Trade-offs 고려사항

**IPsec (Layer 3) 장점**:
- 전체 네트워크 자동 보안 적용
- 애플리케이션 투명성 (프로그램 수정 불필요)
- 강력한 네트워크 간 연결 보안
- 모든 프로토콜 지원

**IPsec 단점**:
- 복잡한 설정과 관리
- 전체 네트워크 의존성
- 높은 전문 지식 요구
- 디버깅의 어려움

**SSL/TLS (Layer 7) 장점**:
- 쉬운 구현과 관리
- 웹 브라우저 기본 지원
- 애플리케이션별 세밀한 제어
- 널리 보급된 표준

**SSL/TLS 단점**:
- 애플리케이션별 개별 적용 필요
- 보안 미적용 프로그램 존재 위험
- CPU 오버헤드 (암호화 처리)
- 애플리케이션 수정 필요한 경우 있음

## 🔍 주요개념

### IPsec vs SSL/TLS 비교

- **IPsec**: 네트워크 계층에서 모든 IP 패킷을 암호화하여 전체 네트워크 트래픽 보호
- **SSL/TLS**: 애플리케이션 계층에서 특정 애플리케이션 데이터만 암호화하여 서비스별 보호

### 실전 적용

- **기업 본사-지사 연결**: IPsec VPN으로 전체 네트워크 연결 보안
- **웹사이트 보안**: SSL/TLS(HTTPS)로 웹 애플리케이션 통신 보안
- **하이브리드 환경**: IPsec으로 네트워크 백본 보안 + SSL/TLS로 개별 서비스 보안

## 📝 관련 문제

**Question:** A financial company needs to secure communication between their headquarters and multiple branch offices for all applications including legacy systems that don't support modern encryption. They also want to ensure their web banking application has additional security. What would be the most appropriate security architecture?

**Options:**

- A) Implement SSL/TLS certificates for all applications including legacy systems
- B) Use IPsec VPN for site-to-site connectivity and SSL/TLS for web banking application
- C) Deploy application-layer firewalls at each branch office only
- D) Implement MACsec encryption on all network switches
- E) Use only network-layer firewalls with port restrictions

**정답: B) IPsec VPN for site-to-site connectivity and SSL/TLS for web banking application**

💡 추가 설명:

- **Option A** - 레거시 시스템은 SSL/TLS를 지원하지 않을 수 있어 모든 애플리케이션에 적용하기 어려움
- **Option C** - 애플리케이션 계층 방화벽만으로는 네트워크 간 암호화된 통신 제공 불가능
- **Option D** - MACsec은 동일 네트워크 내 보안에만 적용되어 원거리 지사 간 연결에 부적합
- **Option E** - 방화벽과 포트 제한만으로는 데이터 암호화 및 무결성 보장 불가능

---