---

title: rekognition-encryption-security-s3
created: 2025-08-27
modified: 2025-08-27
tags:
- service/rekognition
- security/encryption
- constraint/privacy-protection
- storage/s3-encryption
- compliance/data-protection
aliases: ["rekognition-security", "face-comparison-encryption", "aws-ml-privacy"]

---

# Amazon Rekognition 보안 구현을 위한 암호화 및 개인정보 보호 방안

## 🎯 핵심 포인트

얼굴 인식 시스템에서 개인정보 보호가 중요한 경우, S3 서버 측 암호화와 AWS Support를 통한 서비스 개선 사용 제외 신청을 통해 저장 시 암호화, 전송 시 암호화, 데이터 사용 제한을 모두 충족할 수 있다.

## 📝 설명

### Amazon Rekognition이 보안 요구사항 충족에 적합한 이유

Amazon Rekognition은 얼굴 비교 및 인식 서비스를 제공하면서도 엔터프라이즈급 보안 요구사항을 만족할 수 있는 구조를 가지고 있습니다. 특히 다음 세 가지 핵심 보안 요구사항을 모두 만족시킬 수 있습니다:

1. **저장 시 암호화 (Encryption at Rest)**: S3의 서버 측 암호화 기능 활용
2. **전송 시 암호화 (Encryption in Transit)**: AWS API의 기본 HTTPS/TLS 통신
3. **서비스 개선 사용 제외**: AWS Support를 통한 공식적인 opt-out 절차

### 아키텍처 플로우

```
도서관 회원 → 얼굴 촬영
                ↓
         S3 Bucket (서버 측 암호화)
         - KMS 키로 자동 암호화
         - 저장된 얼굴 이미지들
                ↓
         HTTPS/TLS 통신으로 전송
                ↓
    Amazon Rekognition CompareFaces API
    - 실시간 얼굴과 저장된 얼굴 비교
    - 서비스 개선 사용 제외 설정 적용
                ↓
         인증 결과 반환 (도서 대출 승인/거부)
```

### Trade-offs 고려사항

**서버 측 암호화 + AWS Support Opt-out 장점**:
- 기존 시스템 아키텍처 유지 가능
- AWS 표준 보안 기능으로 신뢰성 높음
- 모든 보안 요구사항 충족
- 추가 인프라 구축 불필요
- 비용 효율적

**서버 측 암호화 + AWS Support Opt-out 단점**:
- AWS Support 티켓 처리 시간 필요
- 정책 변경 시 재신청 가능성

**클라이언트 측 암호화 + VPN 장점**:
- 더 강력한 암호화 제어
- 네트워크 레벨 보안 강화

**클라이언트 측 암호화 + VPN 단점**:
- **치명적**: Rekognition이 암호화된 이미지를 직접 처리 불가
- 복호화 과정에서 보안 취약점 발생 가능
- 구현 복잡도 매우 높음
- 추가 인프라 비용 발생

## 🔍 주요개념

### 암호화 방식 비교

- **서버 측 암호화 (SSE)**: S3에서 자동으로 암호화/복호화 처리, 애플리케이션 로직 변경 없음
- **클라이언트 측 암호화 (CSE)**: 클라이언트에서 암호화 후 전송, 서비스에서 암호화된 데이터 처리 불가

### 전송 암호화 방식

- **HTTPS/TLS**: AWS API 기본 제공, 별도 설정 불필요
- **VPN**: 추가 네트워크 보안 레이어, 설정 및 관리 복잡

### 실전 적용

- **도서관 자동 대출 시스템**: 회원 얼굴 데이터베이스와 실시간 비교
- **출입 통제 시스템**: 직원 얼굴 인식을 통한 보안 구역 접근 관리
- **금융 기관 신원 확인**: 고객 신원 확인 과정에서의 얼굴 인증

## 📝 관련 문제

**Question:** A library is developing an automatic book-borrowing system that uses Amazon Rekognition. Images of library members' faces are stored in an Amazon S3 bucket. When members borrow books, the Amazon Rekognition CompareFaces API operation compares real faces against the stored faces in Amazon S3. The library needs to improve security by making sure that images are encrypted at rest. Also, when the images are used with Amazon Rekognition, they need to be encrypted in transit. The library also must ensure that the images are not used to improve Amazon Rekognition as a service. How should a machine learning specialist architect the solution to satisfy these requirements?

**Options:**

- A) Enable server-side encryption on the S3 bucket. Submit an AWS Support ticket to opt out of allowing images to be used for improving the service, and follow the process provided by AWS Support.
- B) Switch to using an Amazon Rekognition collection to store the images. Use the IndexFaces and SearchFacesByImage API operations instead of the CompareFaces API operation.
- C) Switch to using the AWS GovCloud (US) Region for Amazon S3 to store images and for Amazon Rekognition to compare faces. Set up a VPN connection and only call the Amazon Rekognition API operations through the VPN.
- D) Enable client-side encryption on the S3 bucket. Set up a VPN connection and only call the Amazon Rekognition API operations through the VPN.

**정답: A) Enable server-side encryption on the S3 bucket + AWS Support opt-out**

💡 추가 설명:

- **Option B** - Rekognition collection 사용은 서비스 개선 사용 제외 요구사항을 해결하지 못하며, 전송 암호화도 명시적으로 보장되지 않음
- **Option C** - GovCloud와 VPN은 과도한 해결책이며, 서비스 개선 사용 제외 요구사항이 명확히 해결되지 않고 비용과 복잡성만 증가
- **Option D** - 클라이언트 측 암호화된 이미지는 Rekognition이 직접 처리할 수 없어 실용적이지 않으며, 서비스 개선 사용 제외 요구사항도 해결되지 않음