---
title: etl-workflow-step-functions-batch-glue
created: 2025-08-17
modified: 2025-08-17
tags:
- service/step-functions
- service/batch
- service/glue
- usecase/etl-workflow
- constraint/batch-processing
aliases: ["etl-orchestration", "aws-workflow", "batch-etl"]
---

# ETL Workflow with Step Functions, Batch, and Glue

## 🎯 핵심 포인트

다중 AWS 서비스 간 워크플로우를 조율해야 하는 경우 Step Functions에서, 대규모 데이터 결합 및 저장 작업을 Glue로 처리할 수 있다.

## 📝 설명

### AWS Step Functions가 ETL 워크플로우에 적합한 이유
AWS Step Functions는 여러 AWS 서비스(Lambda, Glue, S3, SNS 등)를 연결하고 복잡한 워크플로우를 조율하는 데 최적화된 서버리스 오케스트레이션 서비스다. 질문의 시나리오에서, S3에 데이터가 업로드되면 새 데이터와 기존 데이터를 결합(join)하고, 결과를 별도의 S3 버킷에 저장하며, 실패 시 알림을 보내는 ETL 워크플로우를 구현해야 한다. Step Functions는 상태 머신을 통해 다음을 처리한다:
- **상태 관리**: 데이터 업로드 대기(Wait State), Glue 작업 실행(Task State), 오류 처리(Fail State)를 체계적으로 관리.
- **서비스 통합**: Lambda로 S3 이벤트를 트리거하고, Glue로 ETL 작업을 수행하며, CloudWatch/SNS로 알림 전송.
- **실무 고려사항**: 상태 머신은 JSON 기반의 Amazon States Language(ASL)로 정의되며, 시각적 인터페이스로 워크플로우를 모니터링할 수 있다. 대규모 데이터 처리 시 Glue와의 통합이 간단하며, 서버리스로 작동해 리소스 관리 부담이 없다.

### AWS Glue가 데이터 결합 및 저장에 적합한 이유
AWS Glue는 대규모 ETL 작업(Extract, Transform, Load)에 특화된 서버리스 서비스다. Spark 기반으로 작동하며, S3 데이터를 읽고 변환(예: 데이터 결합)한 뒤 지정된 위치에 저장한다. 시나리오에서:
- **핵심 특징**: 데이터 카탈로그로 S3 데이터의 스키마를 관리하며, TB 단위의 대규모 데이터도 자동 스케일링으로 처리.
- **적용**: 새로 업로드된 S3 데이터와 기존 데이터를 결합(join)하고, 결과를 별도의 S3 버킷에 저장.
- **실무 고려사항**: Glue는 표준 ETL 작업에 최적화되어 있으며, Python(Spark) 또는 GUI로 작업을 정의할 수 있다. 하지만 비표준 데이터 포맷이나 커스텀 로직(예: ML 처리)에는 유연성이 제한적이다.

### AWS Batch가 적합한 경우
AWS Batch는 컴퓨팅 집약적인 단일 작업이나 비표준 데이터 처리가 필요한 경우에 적합하다. 컨테이너(Docker)를 사용해 작업을 실행하며, EC2 또는 Fargate로 리소스를 관리한다. 시나리오에서는 표준 ETL 작업(데이터 결합 및 저장)을 Glue가 처리하므로 Batch는 불필요하지만, 다음과 같은 경우에 유용하다:
- **사용 사례**: 머신 러닝 모델 학습, 미디어 파일 트랜스코딩, 금융 시뮬레이션.
- **실무 고려사항**: Batch는 S3 이벤트에 직접 반응하지 못하므로 Lambda 또는 EventBridge로 트리거해야 하며, 워크플로우 조율은 추가 로직(예: Step Functions)으로 구현해야 한다.

### 아키텍처 플로우

```
S3 데이터 업로드
    ↓
Lambda (S3 이벤트 트리거)
    ↓
Step Functions (워크플로우 시작)
    ↓
Wait State (데이터 업로드 완료 대기)
    ↓
Glue (데이터 결합 및 변환)
    ↓
S3 (결과 저장)
    ↓
CloudWatch Alarms (실패 감지) → SNS (알림 전송)
```

### Trade-offs 고려사항

**AWS Step Functions 장점**:
- 여러 서비스 간 워크플로우를 체계적으로 조율.
- 상태 머신으로 의존성, 재시도, 오류 처리 간단.
- 서버리스로 리소스 관리 부담 없음.

**AWS Step Functions 단점**:
- 복잡한 워크플로우의 경우 상태 머신 정의가 길어질 수 있음.
- 소규모 단일 작업에는 오버헤드 발생 가능.

**AWS Batch 장점**:
- 컴퓨팅 집약적인 작업(예: ML, 미디어 처리)에 적합.
- 컨테이너 기반으로 커스텀 로직 실행 가능.

**AWS Batch 단점**:
- S3 이벤트에 직접 반응하지 못해 Lambda/EventBridge 필요.
- 워크플로우 조율 기능이 없어 추가 설정(예: Step Functions) 요구.
- 표준 ETL 작업은 Glue가 더 간단하고 효율적.

## 🔍 주요개념

### Step Functions vs. Batch 비교

- **Step Functions**: 서버리스 워크플로우 오케스트레이션 서비스. 상태 머신으로 다중 서비스 간 워크플로우를 조율하며, Lambda, Glue, SNS 등과 통합. ETL 워크플로우처럼 순서와 의존성이 중요한 경우 적합.
- **Batch**: 대규모 배치 처리 서비스. 컨테이너로 작업을 실행하며, ML 모델 학습, 미디어 처리, 시뮬레이션 같은 컴퓨팅 집약적인 단일 작업에 강점.

### 실전 적용

- **비디오 스트리밍 서비스**: S3에 업로드된 비디오를 트랜스코딩. Step Functions로 워크플로우 조율, Batch로 비디오 변환 작업 실행.
- **금융 리스크 분석**: 몬테카를로 시뮬레이션을 병렬로 실행. Batch로 시뮬레이션 처리, Step Functions로 결과 저장 및 알림 관리.
- **ETL 워크플로우**: S3 데이터 결합 및 저장. Step Functions로 워크플로우 조율, Glue로 ETL 작업 수행.

## 📝 관련 문제

**Question:** A Machine Learning Specialist needs to design a daily ETL workflow that triggers when data is uploaded to an S3 bucket, joins the new data with pre-existing S3 data, saves the results to a separate S3 bucket, and notifies the specialist if an ETL job fails. What is the most efficient solution?

**Options:**

- A) Execute an AWS Step Functions workflow using a Lambda function, join datasets with AWS Glue, save results to S3, and use CloudWatch Alarms with SNS for notifications.
- B) Trigger a chain of Lambda functions to join datasets, save results to S3, and use CloudWatch Alarms with SNS for notifications.
- C) Use AWS Lambda to launch a SageMaker instance, write a lifecycle script for joining datasets, save results to S3, and use CloudWatch Alarms with SNS for notifications.
- D) Implement the ETL workflow using AWS Batch, trigger it with S3 data upload, join datasets with AWS Glue, save results to S3, and use CloudWatch Alarms with SNS for notifications.
- E) Use AWS Glue to trigger the workflow, join datasets, save results to S3, and use SNS for notifications.

**정답: A) AWS Step Functions with Lambda, Glue, and CloudWatch/SNS**

💡 추가 설명:

- **B) Lambda 체인**: Lambda 체인은 워크플로우 조율이 복잡하고, 상태 추적 및 오류 처리가 어렵다. Lambda의 15분 실행 제한도 대규모 ETL에 부적합.
- **C) SageMaker**: SageMaker는 ML 모델 학습에 특화된 서비스로, ETL 워크플로우 조율이나 데이터 결합에는 비효율적.
- **D) AWS Batch**: Batch는 S3 이벤트에 직접 반응하지 못하며, 표준 ETL 작업은 Glue가 더 적합. 워크플로우 조율을 위해 추가 설정 필요.
- **E) Glue 단독**: Glue는 ETL 작업에 적합하지만, S3 이벤트 트리거와 워크플로우 조율(예: 데이터 대기)을 직접 처리하지 못하므로 Lambda와 Step Functions가 필요.

---