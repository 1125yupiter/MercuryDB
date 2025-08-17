---

title: sagemaker-monitoring-overfitting
created: 2025-08-17
modified: 2025-08-17
tags:
- service/sagemaker
- service/cloudwatch
- service/sns
- monitoring/overfitting
- notification/email
aliases: ["sagemaker-alerts", "model-monitoring", "overfitting-detection"]

---

# SageMaker 모델 오버피팅 모니터링 및 알림 시스템

## 🎯 핵심 포인트

PyTorch 모델을 SageMaker에서 훈련할 때 오버피팅을 실시간으로 감지하려는 경우, 훈련 스크립트에서 CloudWatch API를 통해 메트릭을 전송하고 CloudWatch 알람과 SNS를 연결하여 팀원들에게 이메일 알림을 보낼 수 있다.

## 📝 설명

### CloudWatch가 ML 모델 모니터링에 적합한 이유

CloudWatch는 AWS의 네이티브 모니터링 서비스로, 실시간 메트릭 수집과 알람 기능을 제공한다. 머신러닝 훈련 과정에서 발생하는 커스텀 메트릭(training loss, validation loss, accuracy 등)을 수집하고, 설정된 임계값을 기반으로 자동 알림을 트리거할 수 있다. 특히 오버피팅 감지에 필요한 validation loss 증가 패턴이나 training/validation loss 간의 격차 확대를 실시간으로 모니터링할 수 있다.

### 아키텍처 플로우

```
PyTorch Training Script (SageMaker)
    ↓ (CloudWatch API)
CloudWatch Custom Metrics
    ↓ (Alarm Trigger)
CloudWatch Alarm (Overfitting Condition)
    ↓ (SNS Publish)
SNS Topic
    ↓ (Email Notification)
Team Members' Email Addresses
```

### Trade-offs 고려사항

**CloudWatch + SNS 직접 연결 장점**:
- 실시간 메트릭 수집과 즉시 알림
- 추가 컴퓨팅 리소스 불필요
- AWS 네이티브 서비스로 안정성 보장
- 대시보드를 통한 시각적 모니터링 가능

**CloudWatch + SNS 직접 연결 단점**:
- 복잡한 오버피팅 로직 구현 시 한계
- 알람 조건 설정의 정교함이 제한적

**Lambda 기반 솔루션 장점**:
- 복잡한 비즈니스 로직 구현 가능
- 메트릭 처리 과정에서 추가 가공 가능

**Lambda 기반 솔루션 단점**:
- 불필요한 복잡성 증가
- 추가 비용 발생
- 지연 시간 증가 가능성

## 🔍 주요개념

### 메트릭 수집 방식 비교

- **훈련 스크립트 내 직접 수집**: 훈련 과정에서 실시간으로 메트릭을 CloudWatch로 전송하는 방식. 가장 직접적이고 효율적
- **Lambda를 통한 수집**: 별도의 Lambda 함수가 SageMaker 인스턴스에서 메트릭을 수집하는 방식. 불필요한 복잡성 추가

### 실전 적용

- **실시간 오버피팅 감지**: validation loss가 3 에포크 연속 증가하거나 training loss와의 차이가 임계값을 초과할 때 알림
- **훈련 중단 자동화**: 오버피팅 감지 시 SNS와 Lambda를 연결하여 자동으로 훈련 중단
- **팀 협업 강화**: 데이터 사이언티스트, ML 엔지니어, 프로젝트 매니저가 모두 실시간으로 모델 상태 파악

## 📝 관련 문제

**Question:** A research and development team is training a custom PyTorch model in Amazon SageMaker AI. The team wants to implement a solution where each member will be alerted through email notifications when the model is overfitting. Which solution should the team implement?

**Options:**

- A) Write an AWS Lambda function that uses Amazon CloudWatch API operations to deliver training metrics to the CloudWatch dashboard from the SageMaker instance. Create a CloudWatch alarm that triggers an Amazon SNS topic when a model is overfitting and have each member's email subscribe to the topic.
- B) Use Amazon CloudWatch API operations within the training script to deliver training metrics to CloudWatch. Create a CloudWatch alarm that triggers an SNS topic when a model is overfitting and have each member's email subscribe to the topic.
- C) Create an Amazon EventBridge rule that triggers an Amazon SNS topic when the model is overfitting and have each member's email subscribe to the topic.
- D) Create an Amazon EventBridge rule that triggers an AWS Lambda function when the model is overfitting. Use the Amazon SNS APIs within the function to send email notifications.

**정답: B) Use Amazon CloudWatch API operations within the training script**

💡 추가 설명:

- **Option A** - Lambda를 통한 메트릭 수집은 불필요한 복잡성을 추가한다. 훈련 스크립트에서 직접 CloudWatch API를 호출하는 것이 더 효율적
- **Option C** - EventBridge는 시스템 레벨 이벤트 모니터링에 적합하지만, 모델별 메트릭(training/validation loss)을 자동으로 감지할 수 없어 오버피팅 조건을 판단하는 로직이 부족
- **Option D** - EventBridge와 Lambda 조합은 CloudWatch 알람으로 충분히 해결 가능한 문제에 과도한 복잡성을 추가하며, 오버피팅 감지를 위한 메트릭 수집 방법이 명시되지 않음