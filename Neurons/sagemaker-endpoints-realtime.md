# SageMaker Endpoints - 실시간 Customer Churn 예측 배포

## 🎯 핵심 포인트
실시간 ML 추론이 필요한 경우 SageMaker Endpoints에서, always-on 상태로 밀리초 단위 응답을 제공할 수 있다.

## 📝 설명

### SageMaker Endpoints가 실시간 추론에 적합한 이유
SageMaker Endpoints는 ML 모델을 RESTful API로 배포하여 24/7 always-on 상태를 유지하며, 모델이 항상 메모리에 로드되어 있어 API 호출 시 즉시 응답할 수 있습니다. Auto Scaling 기능으로 트래픽에 따라 자동으로 인스턴스를 확장/축소하여 고가용성과 저지연을 보장합니다.

### 실시간 아키텍처 플로우
```
[고객 웹/앱] → [백엔드 API] → [SageMaker Endpoint] → [실시간 응답]
고객 행동 감지 → 추론 요청 → 이탈 확률 계산 → 즉시 대응 (할인 쿠폰 등)
```

### Trade-offs 고려사항
**SageMaker Endpoints 장점**: 저지연 실시간 응답, Auto Scaling, 고가용성, 모델 모니터링
**SageMaker Endpoints 단점**: 24/7 운영 비용, 인프라 관리 필요

**AWS Lambda 장점**: 서버리스, 사용한만큼 과금, 관리 부담 없음
**AWS Lambda 단점**: Cold Start 지연(수초), 15분 실행 제한, 실시간 부적합

## 🔍 주요개념

### Batch vs Real-time 차이
- **Real-time**: 요청 즉시 응답 (밀리초 단위), 개별 요청 처리
- **Batch**: 대량 데이터 일괄 처리, 스케줄 기반, 시간당/일당 처리

### 실전 적용
- E-commerce에서 고객 이탈 감지 시 즉시 할인 쿠폰 제공
- 실시간 사기 거래 탐지 및 차단
- 개인화 추천 시스템의 실시간 컨텐츠 제공

## 📝 관련 시험 문제

**Question:** You have developed a machine learning model to predict customer churn for an e-commerce company, and you need to deploy the model to process new data in real-time. Which of the following AWS services would be the best fit for this use case?

**Options:**
- A) Amazon SageMaker Batch Transform
- B) AWS Lambda
- C) Amazon SageMaker Endpoints
- D) Amazon SageMaker Neo
- E) AWS Batch

**정답: C) Amazon SageMaker Endpoints**


💡 추가 설명:
• SageMaker Batch Transform - 대량 배치 처리 전용, 실시간 처리 불가
• AWS Lambda - Cold Start 이슈(수초 지연)로 실시간 추론 부적합  
• SageMaker Neo - 엣지/모바일 디바이스용 모델 최적화 도구
• AWS Batch - 대규모 배치 작업용 서비스, 스케줄 기반 실행

## 🏷️ 태그
`#service:sagemaker-endpoints` `#constraint:real-time` `#deployment:always-on` `#latency:milliseconds` `#usecase:customer-churn`