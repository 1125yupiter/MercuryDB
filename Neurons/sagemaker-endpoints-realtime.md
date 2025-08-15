---
title: sagemaker-endpoints-realtime-inference-always-on-milliseconds
created: 2025-08-15
modified: 2025-08-15
tags:
- service/sagemaker-endpoints
- constraint/real-time
- deployment/always-on
- latency/milliseconds
- usecase/customer-churn
aliases: ["SageMaker Endpoints", "실시간 ML 추론", "Customer Churn 예측"]
---

# SageMaker Endpoints - 실시간 Customer Churn 예측 배포

## 🎯 핵심 포인트
실시간 ML 추론이 필요한 경우 SageMaker Endpoints에서, always-on 상태로 밀리초 단위 응답을 제공할 수 있다.

## 📝 설명

### SageMaker Endpoints가 실시간 추론에 적합한 이유
SageMaker Endpoints는 ML 모델을 RESTful API로 배포하여 24/7 always-on 상태를 유지하며, 모델이 항상 메모리에 로드되어 있어 API 호출 시 즉시 응답할 수 있습니다. Auto Scaling 기능으로 트래픽에 따라 자동으로 인스턴스를 확장/축소하여 고가용성과 저지연을 보장하며, CloudWatch를 통한 실시간 모니터링과 A/B 테스팅을 위한 Multi-Model Endpoints 기능도 제공합니다.

### 아키텍처 플로우
```
[고객 웹/앱] → [백엔드 API] → [SageMaker Endpoint] → [실시간 응답]
      ↓              ↓               ↓              ↓
  고객 행동 감지  →  추론 요청   →   이탈 확률 계산  →  즉시 대응
                                                  (할인 쿠폰/맞춤 제안)
```

### Trade-offs 고려사항
**SageMaker Endpoints 장점**:
- 밀리초 단위 저지연 실시간 응답
- Auto Scaling을 통한 고가용성
- 내장된 모델 모니터링 및 A/B 테스팅
- Multi-AZ 배포를 통한 장애 복구

**SageMaker Endpoints 단점**:
- 24/7 인스턴스 운영에 따른 고정 비용
- 트래픽이 적을 때도 최소 인스턴스 유지 필요
- 인프라 설정 및 관리 복잡성

**AWS Lambda 장점**:
- 서버리스로 인프라 관리 불필요
- 사용한 만큼만 과금되는 비용 효율성
- 자동 확장 및 고가용성

**AWS Lambda 단점**:
- Cold Start로 인한 수초 지연 발생
- 15분 실행 시간 제한으로 실시간 부적합
- 대용량 모델 로딩 시간 지연

## 🔍 주요개념

### 실시간 vs 배치 추론 비교
- **Real-time**: 요청 즉시 응답 (밀리초 단위), 개별 요청 처리, 사용자 대기 상태
- **Batch**: 대량 데이터 일괄 처리, 스케줄 기반 실행, 시간당/일당 주기적 처리

### 실전 적용
- E-commerce에서 고객 장바구니 이탈 감지 시 즉시 할인 쿠폰 또는 맞춤 제안 제공
- 금융 서비스에서 실시간 사기 거래 탐지 및 자동 차단 시스템
- 스트리밍 플랫폼에서 사용자 행동 기반 실시간 개인화 추천 컨텐츠 제공

## 📝 관련 문제

**Question:** You have developed a machine learning model to predict customer churn for an e-commerce company, and you need to deploy the model to process new data in real-time. Which of the following AWS services would be the best fit for this use case?

**Options:**
- A) Amazon SageMaker Batch Transform
- B) AWS Lambda
- C) Amazon SageMaker Endpoints
- D) Amazon SageMaker Neo
- E) AWS Batch

**정답: C) Amazon SageMaker Endpoints**

💡 추가 설명:
- **A) SageMaker Batch Transform** - 대량 데이터 배치 처리 전용으로 실시간 처리 불가
- **B) AWS Lambda** - Cold Start 이슈(수초 지연)로 실시간 추론에 부적합
- **D) SageMaker Neo** - 엣지/모바일 디바이스용 모델 최적화 도구, 배포 서비스 아님
- **E) AWS Batch** - 대규모 배치 작업 처리용 서비스로 스케줄 기반 실행