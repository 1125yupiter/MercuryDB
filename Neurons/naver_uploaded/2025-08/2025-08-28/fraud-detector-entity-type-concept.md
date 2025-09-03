---

title: fraud-detector-entity-type-concept
created: 2025-08-19
modified: 2025-08-19
tags:
- service/fraud-detector
- concept/entity-type
- ml/fraud-detection
- aws/managed-service
- security/fraud-prevention
aliases: ["fraud detector", "entity type", "amazon fraud detector"]

---

# Amazon Fraud Detector의 Entity Type 개념과 역할

## 🎯 핵심 포인트

사기 탐지 시스템을 구축한 경우 Amazon Fraud Detector에서, Entity Type을 통해 탐지기가 처리할 엔티티(고객, 거래 등)의 유형과 속성을 정의할 수 있다.

## 📝 설명

### Amazon Fraud Detector가 사기 탐지에 적합한 이유

Amazon Fraud Detector는 완전 관리형 사기 탐지 서비스로, Entity Type이라는 핵심 개념을 통해 사기 탐지 대상을 명확히 정의합니다. Entity Type은 탐지기가 분석할 엔티티의 유형을 정의하며, 각 엔티티는 고유한 속성 필드들을 가집니다. 이를 통해 머신러닝 모델이 특정 엔티티의 패턴을 학습하고 사기를 탐지할 수 있습니다.

### 아키텍처 플로우

```
Entity Type 정의
↓
속성 필드 설정 (ID, 이메일, 거래금액 등)
↓
훈련 데이터셋 준비
↓
ML 모델 학습
↓
Detector 생성 및 Entity Type 연결
↓
실시간 사기 탐지 수행
```

### Trade-offs 고려사항

**Amazon Fraud Detector 장점**:
- 완전 관리형 서비스로 인프라 관리 불필요
- 사전 훈련된 모델과 사용자 정의 모델 모두 지원
- 실시간 추론 및 배치 처리 지원
- AWS 서비스와의 원활한 통합

**Amazon Fraud Detector 단점**:
- 복잡한 초기 설정 과정
- 상대적으로 높은 비용
- 제한적인 커스터마이징 옵션

**자체 구축 솔루션 장점**:
- 완전한 제어권과 유연성
- 특정 요구사항에 맞춤 설계 가능

**자체 구축 솔루션 단점**:
- 높은 개발 및 유지보수 비용
- 전문 인력 필요
- 인프라 관리 부담

## 🔍 주요개념

### 핵심 구성 요소 비교

- **Entity Type**: 탐지기가 처리할 엔티티의 유형을 정의 (예: customer, transaction, account)
- **Event Type**: 분석할 이벤트의 종류를 정의 (예: payment, registration, login)
- **Model Type**: 사용할 머신러닝 모델의 유형 결정 (예: supervised, unsupervised)
- **Detector**: 실제 사기 탐지를 수행하는 구성 요소

### 실전 적용

- **전자상거래**: Customer Entity Type으로 계정 생성, 주문 패턴 분석
- **금융 서비스**: Transaction Entity Type으로 결제 거래, 자금 이체 모니터링
- **게임 산업**: Player Entity Type으로 게임 내 행동, 아이템 거래 추적

## 📝 관련 문제

**Question:** A company wants to implement fraud detection for their e-commerce platform. They need to monitor customer behavior patterns, transaction amounts, and account activities. What should they define first in Amazon Fraud Detector to establish the foundation for their fraud detection system?

**Options:**

- A) The machine learning model type that will be used for fraud detection
- B) The input data format to be used in the detector models  
- C) The output format of the fraud detection results
- D) The type of entity that the detector will process, such as customer or transaction
- E) The event types that will trigger the fraud detection process

**정답: D) The type of entity that the detector will process, such as customer or transaction**

💡 추가 설명:

- **A) ML 모델 타입** - Entity Type 정의 후에 결정되는 사항으로, 기초 구조 설정이 아님
- **B) 입력 데이터 형식** - Data Schema 설정 영역으로, Entity Type의 역할이 아님
- **C) 출력 형식** - Prediction 결과 포맷 관련으로, 기초 설정 단계가 아님
- **E) 이벤트 타입** - Event Type은 Entity Type 정의 후 설정하는 구성 요소임