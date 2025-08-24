---

title: rekognition-security-encryption-vpn
created: 2025-08-24
modified: 2025-08-24
tags:
- service/rekognition
- constraint/encryption-at-rest
- constraint/encryption-in-transit
- security/client-side-encryption
- usecase/face-recognition
aliases: ["face-recognition-security", "rekognition-encryption", "library-security"]

---

# Amazon Rekognition을 활용한 도서관 얼굴 인식 시스템의 보안 강화

## 🎯 핵심 포인트

얼굴 인식 기반 도서 대출 시스템에서 저장 시 암호화, 전송 시 암호화, 서비스 개선 데이터 사용 방지가 모두 필요한 경우 S3 client-side encryption과 VPN 연결을 결합하여 완전한 보안을 구현할 수 있다.

## 📝 설명

### Amazon Rekognition이 얼굴 인식 시스템에 적합한 이유

Amazon Rekognition의 CompareFaces API는 실시간 얼굴 비교를 통해 도서관 회원의 신원 확인을 제공합니다. S3에 저장된 기준 얼굴 이미지와 현재 촬영된 얼굴을 비교하여 높은 정확도의 인증을 수행할 수 있습니다. 하지만 민감한 생체정보를 다루기 때문에 저장 시 암호화, 전송 시 암호화, 그리고 AWS 서비스 개선에 데이터가 사용되지 않도록 하는 3중 보안이 필요합니다.

### 아키텍처 플로우

```
회원 얼굴 등록/인증 플로우:

1. 클라이언트에서 얼굴 이미지 암호화
2. 암호화된 이미지를 S3에 저장
3. 인증 시 VPN을 통해 Rekognition API 호출
4. 클라이언트에서 복호화 후 CompareFaces 실행
5. 결과를 VPN을 통해 안전하게 전송

[클라이언트] ---(암호화된 이미지)---> [S3 Bucket]
     |                                      |
     |---(VPN 연결)---> [Rekognition API] --|
     |
     |---(복호화된 결과)---> [도서 대출 시스템]
```

### Trade-offs 고려사항

**Client-side Encryption 장점**:
- AWS도 원본 이미지에 접근할 수 없어 최고 수준의 보안 제공
- 서비스 개선에 데이터 사용 기술적 차단
- 클라이언트가 암호화 키를 완전 통제

**Client-side Encryption 단점**:
- 구현 복잡도 증가 및 성능 오버헤드
- 키 관리의 책임이 클라이언트에게 전가
- 암호화/복호화 과정에서 추가 지연 발생

**Server-side Encryption 장점**:
- 구현이 간단하고 AWS가 키 관리
- 성능 오버헤드가 상대적으로 적음

**Server-side Encryption 단점**:
- AWS가 여전히 원본 데이터에 접근 가능
- 서비스 개선 사용 방지를 위해 별도 AWS Support 요청 필요

## 🔍 주요개념

### 암호화 방식 비교

- **Client-side Encryption**: 클라이언트에서 암호화 후 S3 업로드. AWS는 암호화된 데이터만 접근 가능
- **Server-side Encryption**: S3에서 자동 암호화 수행. AWS가 원본 데이터에 접근 가능

### 전송 보안 방식

- **VPN 연결**: 전용 암호화 터널을 통한 API 호출로 전송 구간 보안 강화
- **HTTPS**: 기본 웹 암호화이지만 공용 인터넷 사용으로 상대적으로 보안 수준이 낮음

### 실전 적용

- 은행 지점의 ATM 얼굴 인증 시스템에서 고객 생체정보 보호
- 병원의 환자 신원 확인 시스템에서 의료 정보 보안 준수
- 정부 기관의 출입 통제 시스템에서 국가 보안 요구사항 충족

## 📝 관련 문제

**Question:** A library is creating an Amazon Rekognition-based automated book-borrowing system. Amazon S3 buckets are used to store images of library users' faces. When members borrow books, the Amazon Rekognition CompareFaces API matches their actual faces to those stored in Amazon S3. The library's security should be strengthened by ensuring that photos are encrypted at rest. Additionally, when photos are utilized in conjunction with Amazon Rekognition, they must be secured during transport. Additionally, the library must verify that no photos are utilized to enhance Amazon Rekognition as a service. How might an expert in machine learning construct a system that satisfies these requirements?

**Options:**

- A) Enable server-side encryption on the S3 bucket. Submit an AWS Support ticket to opt out of allowing images to be used for improving the service, and follow the process provided by AWS Support.
- B) Switch to using an Amazon Rekognition collection to store the images. Use the IndexFaces and SearchFacesByImage API operations instead of the CompareFaces API operation.
- C) Switch to using the AWS GovCloud (US) Region for Amazon S3 to store images and for Amazon Rekognition to compare faces. Set up a VPN connection and only call the Amazon Rekognition API operations through the VPN.
- D) Enable client-side encryption on the S3 bucket. Set up a VPN connection and only call the Amazon Rekognition API operations through the VPN.

**정답: D) Client-side encryption + VPN 연결**

💡 추가 설명:

- **A번** - 서버 사이드 암호화는 저장 시 암호화를 해결하지만 전송 시 암호화가 누락됨. 일반 인터넷을 통한 API 호출로 전송 보안 미흡
- **B번** - Rekognition Collection 사용은 보안 요구사항과 무관한 기능적 변경. 암호화 요구사항을 전혀 해결하지 못함
- **C번** - GovCloud 리전만으로는 저장 시 암호화가 자동 보장되지 않으며, 서비스 개선 사용 방지 조치가 누락됨