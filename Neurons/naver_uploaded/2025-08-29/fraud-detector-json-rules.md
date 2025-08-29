---

title: fraud-detector-json-rules
created: 2025-08-20
modified: 2025-08-20
tags:
- service/fraud-detector
- format/json
- usecase/fraud-detection
- constraint/rule-based
- aws/security
aliases: ["fraud-detector", "json-rules", "amazon-fraud-detector"]

---

# Amazon Fraud Detector에서 JSON 규칙 언어를 활용한 사기 탐지 시스템

## 🎯 핵심 포인트

신용카드 거래 사기를 탐지해야 하는 경우 Amazon Fraud Detector에서, JSON 형식의 규칙 언어를 사용하여 거래 금액, 판매자 카테고리 코드, 카드 발급사 등의 조건을 정의할 수 있다.

## 📝 설명

### Amazon Fraud Detector가 사기 탐지에 적합한 이유

Amazon Fraud Detector는 머신러닝 모델과 규칙 기반 시스템을 결합하여 실시간 사기 탐지를 제공하는 완전관리형 서비스입니다. JSON 형식의 규칙 언어를 사용하는 이유는 다음과 같습니다:

- **인간 친화적**: JSON은 가독성이 높아 비즈니스 사용자도 쉽게 이해할 수 있음
- **머신 파싱**: 자동화된 시스템에서 규칙을 쉽게 해석하고 실행 가능
- **유연성**: 복합 조건과 논리 연산자를 통해 복잡한 사기 패턴 정의 가능
- **실시간 처리**: 거래 발생 시점에 즉시 규칙을 적용하여 판단

### 아키텍처 플로우

```
거래 데이터 입력
    ↓
JSON 규칙 엔진
    ↓
조건 평가 (거래금액, 판매자코드, 카드발급사)
    ↓
결과 출력 (승인/차단/조사필요)
    ↓
비즈니스 액션 실행
```

### Trade-offs 고려사항

**JSON 규칙 장점**:
- 직관적이고 이해하기 쉬운 문법
- 빠른 규칙 수정과 배포 가능
- 복합 조건 지원으로 정교한 탐지 규칙 작성
- AWS 생태계와의 완벽한 통합

**JSON 규칙 단점**:
- 정적 규칙으로 새로운 사기 패턴 적응에 시간 소요
- 복잡한 로직은 가독성 저하 가능성
- 규칙 수가 많아질 경우 관리 복잡성 증가

**다른 언어들의 한계**:
- **Python/Ruby/JavaScript**: 프로그래밍 언어는 Fraud Detector에서 직접 지원하지 않음
- **SQL**: 데이터베이스 쿼리 언어로 실시간 규칙 엔진에 부적합

## 🔍 주요개념

### 규칙 구성 요소

- **rule_id**: 각 규칙의 고유 식별자
- **expression**: 조건을 정의하는 논리 표현식
- **outcomes**: 규칙 매치 시 실행할 액션 정의

### 실전 적용

- **고액 거래 탐지**: `$transaction_amount > 5000`으로 임계값 기반 탐지
- **의심스러운 판매자**: 특정 카테고리 코드와 거래 금액 조합으로 위험 거래 식별
- **시간대별 패턴**: 심야 시간대 해외 카드 사용 등 비정상 패턴 탐지

## 📝 관련 문제

**Question:** You are building a fraud detection system using Amazon Fraud Detector. Your dataset contains information on credit card transactions, including the transaction amount, the merchant category code, and the card issuer. You have trained a machine learning model and are now ready to deploy it as a detector. Which of the following is a valid rule language that can be used to define your fraud detection rules in Amazon Fraud Detector?

**Options:**

- A) Python
- B) Ruby  
- C) JavaScript
- D) SQL
- E) JSON

**정답: E) JSON**

💡 추가 설명:

- **Python** - 프로그래밍 언어이지만 Fraud Detector의 규칙 정의 언어로는 지원되지 않음
- **Ruby** - 마찬가지로 일반 프로그래밍 언어로 규칙 엔진에서 사용 불가
- **JavaScript** - 웹 개발 언어이지만 AWS Fraud Detector 규칙 정의에는 부적합
- **SQL** - 데이터베이스 쿼리 언어로 실시간 규칙 평가 시스템에는 적합하지 않음

## 💻 JSON 규칙 예시

### 기본 고액 거래 탐지
```json
{
  "rule_id": "high_amount_transaction",
  "expression": "$transaction_amount > 5000",
  "outcomes": ["high_risk"]
}
```

### 복합 조건 규칙
```json
{
  "rule_id": "suspicious_merchant_pattern",
  "expression": "$transaction_amount > 1000 AND $merchant_category_code in ['5813', '7995', '5912']",
  "outcomes": ["investigate"]
}
```

### 시간대와 카드 발급사 조합
```json
{
  "rule_id": "foreign_card_night_purchase",
  "expression": "$transaction_amount > 2000 AND $card_issuer != 'domestic' AND $transaction_time between '22:00' and '06:00'",
  "outcomes": ["block_transaction"]
}
```