---

title: etl-stepfunctions-glue-workflow
created: 2025-08-25
modified: 2025-08-25
tags:
- service/stepfunctions
- service/glue
- workflow/orchestration
- constraint/multiple-datasets
- usecase/etl-pipeline
aliases: ["ETL Workflow", "Step Functions ETL", "Multi-Dataset Processing"]

---

# AWS Step Functions와 Glue를 활용한 다중 데이터셋 ETL 워크플로우

## 🎯 핵심 포인트

여러 데이터셋이 모두 준비된 후 테라바이트급 ETL 작업을 수행해야 하는 경우, Step Functions로 워크플로우를 오케스트레이션하고 Glue로 실제 데이터 조인을 처리할 수 있다.

## 📝 설명

### Step Functions + Glue가 복잡한 ETL 워크플로우에 적합한 이유

**Step Functions**는 여러 AWS 서비스를 연결하여 복잡한 워크플로우를 관리하는 서버리스 오케스트레이션 서비스입니다. 특히 조건부 대기, 상태 추적, 오류 처리가 필요한 시나리오에서 강력한 기능을 제공합니다.

**AWS Glue**는 Apache Spark 기반의 완전 관리형 ETL 서비스로, 테라바이트급 대용량 데이터 처리에 최적화되어 있습니다. 서버리스 환경에서 자동 스케일링을 통해 비용 효율적인 데이터 변환을 수행할 수 있습니다.

### 아키텍처 플로우

```
S3 데이터셋 A 업로드 → Lambda 트리거 → Step Functions 시작
                                      ↓
S3 데이터셋 B 업로드 → Lambda 트리거 → [상태 업데이트]
                                      ↓
S3 데이터셋 C 업로드 → Lambda 트리거 → [모든 데이터셋 확인]
                                      ↓
                              [조건 만족 시 Glue ETL 실행]
                                      ↓
                              [테라바이트급 데이터 조인 처리]
                                      ↓
                              [결과를 S3에 저장]
                                      ↓
                         [실패 시 CloudWatch → SNS 알림]
```

### Trade-offs 고려사항

**Step Functions + Glue 장점**:
- 복잡한 워크플로우 로직을 시각적으로 관리 가능
- 서버리스 환경으로 인프라 관리 불필요
- 테라바이트급 데이터 처리에 최적화된 성능
- 자동 스케일링으로 비용 효율성 확보
- 상태 추적 및 오류 처리 기능 내장

**Step Functions + Glue 단점**:
- 워크플로우 복잡성 증가 시 디버깅 어려움
- Cold Start 지연 시간 존재
- 실시간 처리보다는 배치 처리에 적합

**AWS Batch 장점**:
- 컨테이너 기반으로 환경 자유도 높음
- CPU 집약적 계산 작업에 특화
- 대량 병렬 처리 최적화

**AWS Batch 단점**:
- 이벤트 기반 트리거 구성 복잡
- 조건부 워크플로우 로직 구현 어려움
- 상태 관리 기능 부족

## 🔍 주요개념

### 서비스별 역할 분담

- **Step Functions**: 워크플로우 오케스트레이션, 조건부 대기, 상태 관리, 오류 처리
- **AWS Glue**: 실제 ETL 작업 실행, 대용량 데이터 조인, Spark 기반 처리
- **AWS Batch**: 순수 계산 작업의 대량 병렬 처리 (CPU 집약적)
- **Lambda**: 가벼운 트리거 및 간단한 처리 로직

### 실전 적용 시나리오

- **일일 데이터 파이프라인**: 여러 소스에서 오는 데이터를 모두 수집한 후 통합 분석
- **금융 리스크 분석**: 시장 데이터, 거래 데이터, 고객 데이터가 모두 준비된 후 종합 분석
- **IoT 센서 데이터 통합**: 다양한 센서에서 오는 데이터를 시간대별로 수집하여 통합 처리

## 📝 관련 문제

**Question:** A Machine Learning Specialist is developing a daily ETL workflow containing multiple ETL jobs. The workflow consists of the following processes: Start the workflow as soon as data is uploaded to Amazon S3. When all the datasets are available in Amazon S3, start an ETL job to join the uploaded datasets with multiple terabyte-sized datasets already stored in Amazon S3. Store the results of joining datasets in Amazon S3. If one of the jobs fails, send a notification to the Administrator. Which configuration will meet these requirements?

**Options:**

- A) Use AWS Lambda to trigger an AWS Step Functions workflow to wait for dataset uploads to complete in Amazon S3. Use AWS Glue to join the datasets. Use an Amazon CloudWatch alarm to send an SNS notification to the Administrator in the case of a failure.
- B) Develop the ETL workflow using AWS Lambda to start an Amazon SageMaker notebook instance. Use a lifecycle configuration script to join the datasets and persist the results in Amazon S3. Use an Amazon CloudWatch alarm to send an SNS notification to the Administrator in the case of a failure.
- C) Develop the ETL workflow using AWS Batch to trigger the start of ETL jobs when data is uploaded to Amazon S3. Use AWS Glue to join the datasets in Amazon S3. Use an Amazon CloudWatch alarm to send an SNS notification to the Administrator in the case of a failure.
- D) Use AWS Lambda to chain other Lambda functions to read and join the datasets in Amazon S3 as soon as the data is uploaded to Amazon S3. Use an Amazon CloudWatch alarm to send an SNS notification to the Administrator in the case of a failure.

**정답: A) AWS Lambda + Step Functions + Glue 조합**

💡 추가 설명:

- **B) SageMaker 옵션** - SageMaker는 머신러닝 모델 훈련/추론용이며 ETL 작업에는 부적합
- **C) Batch 옵션** - 이벤트 기반 트리거와 조건부 대기 로직 구현이 복잡하고 워크플로우 오케스트레이션 기능 부족
- **D) Lambda 체이닝** - Lambda의 15분 실행 제한과 메모리 제한으로 테라바이트급 데이터 처리 불가능