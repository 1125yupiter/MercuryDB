---

title: sagemaker-monitoring-cloudtrail
created: 2025-08-24
modified: 2025-08-24
tags:
- service/cloudtrail
- service/sagemaker
- monitoring/overfitting
- logging/api-calls
- notification/sns
aliases: ["sagemaker-logs", "ml-monitoring", "cloudtrail-audit"]

---

# SageMaker 모델 모니터링 및 API 로깅 솔루션

## 🎯 핵심 포인트

SageMaker에서 모델 오버피팅 알림이 필요하고 API 호출 감사가 필요한 경우, CloudTrail과 CloudWatch를 결합하여 최소한의 코드로 모니터링 및 로깅을 구현할 수 있다.

## 📝 설명

### CloudTrail이 SageMaker API 감사에 적합한 이유

AWS CloudTrail은 모든 AWS 서비스의 API 호출을 자동으로 로깅하는 서비스로, SageMaker API 호출을 별도의 Lambda 함수 없이도 S3에 자동 저장한다. 감사 목적으로는 CloudTrail이 가장 표준적이고 안전한 솔루션이며, 추가적인 코드 작성이나 관리가 필요하지 않다.

### 아키텍처 플로우

```
SageMaker Training Job → CloudTrail (API 로깅) → S3 (로그 저장)
                    ↓
Training Script → Custom Metric → CloudWatch → SNS (오버피팅 알림)
```

### Trade-offs 고려사항

**CloudTrail 장점**:
- AWS 네이티브 감사 솔루션으로 신뢰성 높음
- 별도 코드 구현 없이 모든 API 호출 자동 로깅
- 규정 준수 및 감사 요구사항에 완벽 대응
- S3에 안전하게 장기 보관 가능

**CloudTrail 단점**:
- 실시간성 부족 (약간의 지연 발생)
- 로그 볼륨이 클 경우 비용 증가

**Lambda 함수 기반 로깅 장점**:
- 실시간 처리 가능
- 커스터마이징 자유도 높음

**Lambda 함수 기반 로깅 단점**:
- 추가 코드 작성 및 유지보수 필요
- CloudTrail 대비 신뢰성 낮음
- 감사 목적으로는 부적합 (우회 가능)

## 🔍 주요개념

### CloudTrail vs Lambda 로깅

- **CloudTrail**: AWS 네이티브 감사 서비스, 모든 API 호출 자동 추적, 감사 표준
- **Lambda 로깅**: 커스텀 로깅 솔루션, 추가 구현 필요, 실시간 처리 가능

### 실전 적용

- 규정 준수가 필요한 금융/의료 분야에서 ML 모델 개발 시 API 감사
- 실시간 모델 성능 모니터링과 히스토리컬 API 감사가 동시에 필요한 경우
- 최소한의 운영 오버헤드로 완전한 감사 트레일이 필요한 프로덕션 환경

## 📝 관련 문제

**Question:** A Machine Learning team trains an Apache MXNet handwritten digit classification model using Amazon SageMaker and wants to be notified when the model becomes overfit. Auditors wish to inspect the Amazon SageMaker log activity report to guarantee no illegal API calls have occurred. What should the team do to ensure criteria are met with the fewest lines of code and procedures?

**Options:**

- A) Implement an AWS Lambda function to log Amazon SageMaker API calls to Amazon S3. Add code to push a custom metric to Amazon CloudWatch. Create an alarm in CloudWatch with Amazon SNS to receive a notification when the model is overfitting.
- B) Use AWS CloudTrail to log Amazon SageMaker API calls to Amazon S3. Add code to push a custom metric to Amazon CloudWatch. Create an alarm in CloudWatch with Amazon SNS to receive a notification when the model is overfitting.
- C) Implement an AWS Lambda function to log Amazon SageMaker API calls to AWS CloudTrail. Add code to push a custom metric to Amazon CloudWatch. Create an alarm in CloudWatch with Amazon SNS to receive a notification when the model is overfitting.
- D) Use AWS CloudTrail to log Amazon SageMaker API calls to Amazon S3. Set up Amazon SNS to receive a notification when the model is overfitting.

**정답: B) Use AWS CloudTrail**

💡 추가 설명:

- **옵션 A** - Lambda 함수로 API 로깅 시 불필요한 코드 작성 및 관리 오버헤드 발생, 감사 목적으로 부적합
- **옵션 C** - CloudTrail은 Lambda 함수가 아닌 AWS 네이티브 서비스로, 구현 방식이 잘못됨
- **옵션 D** - 오버피팅 감지를 위한 CloudWatch 커스텀 메트릭 및 알람 설정이 누락됨