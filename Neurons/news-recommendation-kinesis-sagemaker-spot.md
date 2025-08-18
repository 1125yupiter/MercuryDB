---

title: news-recommendation-kinesis-sagemaker-spot
created: 2025-08-18
modified: 2025-08-18
tags:
- service/kinesis-firehose
- service/sagemaker-spot
- usecase/recommendation
- constraint/cost-effective
- constraint/traffic-spike
aliases: ["news-ml-arch", "personalized-recommendation", "kinesis-sagemaker"]

---

# 뉴스 포털 개인화 추천을 위한 AWS ML 아키텍처

## 🎯 핵심 포인트

선거와 같은 주요 이벤트로 트래픽이 급증하는 뉴스 포털에서 일일 머신러닝 모델 훈련을 통한 개인화 추천을 구현할 경우, Kinesis Data Firehose → S3 → SageMaker Managed Spot Training → DynamoDB 아키텍처를 사용할 수 있다.

## 📝 설명

### Kinesis Data Firehose + SageMaker Spot Training이 뉴스 포털 추천에 적합한 이유

이 아키텍처는 **불필요한 복잡성을 제거**하고 **비용 효율성**에 집중한 설계입니다. 뉴스 포털의 클릭스트림 데이터는 이미 구조화되어 있어 복잡한 ETL 없이 직접 활용할 수 있으며, 일일 배치 훈련이면 충분한 상황에서 실시간 처리는 불필요한 오버엔지니어링입니다.

**핵심 설계 원칙:**
- 데이터 수집과 저장의 분리로 안정성 확보
- Spot Training으로 최대 90% 비용 절감
- DynamoDB로 밀리초 단위 추천 응답
- 트래픽 급증 시 자동 스케일링

### 아키텍처 플로우

```
클릭스트림 데이터 
    ↓
Kinesis Data Firehose (자동 배치, 스케일링)
    ↓
Amazon S3 (데이터 레이크, 내구성)
    ↓
SageMaker Managed Spot Training (일일 배치, 90% 비용 절감)
    ↓
DynamoDB (실시간 추천 서빙, 밀리초 응답)
    ↓
사용자에게 개인화 추천
```

### Trade-offs 고려사항

**Kinesis Data Firehose 장점**:
- 자동 스케일링으로 트래픽 급증 대응
- 사용한 만큼만 과금으로 비용 효율적
- 설정 간단, 운영 부담 최소화
- S3 직접 전송으로 데이터 손실 방지

**Kinesis Data Firehose 단점**:
- 실시간성보다 배치 처리에 최적화
- 복잡한 데이터 변환 시 제약

**SageMaker Spot Training 장점**:
- On-Demand 대비 최대 90% 비용 절감
- 중단 시 자동 복구 및 재시작
- 관리형 서비스로 인프라 운영 불필요

**SageMaker Spot Training 단점**:
- 인스턴스 중단 가능성 (자동 복구되지만 지연)
- 즉시 시작이 필요한 워크로드에는 부적합

**MSK + Aurora 대안의 장점**:
- 실시간 스트리밍 처리 가능
- 복잡한 쿼리 지원

**MSK + Aurora 대안의 단점**:
- 일일 배치면 충분한 상황에서 과도한 복잡성
- 높은 비용과 운영 부담
- 뉴스 추천에 불필요한 기능들

## 🔍 주요개념

### 데이터 수집 방식 비교

- **Kinesis Data Firehose**: 관리형 배치 전송, 자동 스케일링, S3 최적화
- **Kinesis Data Streams**: 실시간 스트리밍, 수동 스케일링, 개발자 제어 필요
- **MSK (Managed Kafka)**: 복잡한 스트리밍 워크플로우, 높은 처리량

### 모델 훈련 비용 최적화

- **Spot Training**: 중단 가능하지만 자동 복구, 최대 90% 비용 절감
- **On-Demand**: 안정적이지만 높은 비용
- **Reserved**: 장기 사용 시 할인, 유연성 제한

### 실전 적용

- **뉴스 포털**: 클릭스트림 기반 개인화 추천, 선거철 트래픽 급증 대응
- **전자상거래**: 구매 이력 기반 상품 추천, 블랙프라이데이 등 이벤트 대응
- **스트리밍 서비스**: 시청 패턴 기반 콘텐츠 추천, 인기 콘텐츠 출시 시 급증 대응

## 📝 관련 문제

**Question:** A leading news portal seeks to deliver personalized article recommendations by daily training a machine learning model using historical clickstream data. The volume of incoming data is consistent but experiences substantial spikes during major elections, leading to increased site traffic. Which architecture would ensure the most cost-effective and reliable framework for accommodating these conditions?

**Options:**

- A) Capture clickstream data using Amazon Kinesis Data Firehose to Amazon S3. Process the data with Amazon SageMaker for model training using Managed Spot Training. Publish results to Amazon DynamoDB for instant recommendation serving.
- B) Route clickstream data to Amazon Managed Streaming for Apache Kafka (Amazon MSK), then process in real-time with Amazon SageMaker for predictive modeling. Persist model insights in Amazon Aurora for delivering real-time content recommendations.
- C) Stream clickstream data into Amazon S3 via Amazon Kinesis Data Streams, then use AWS Glue for real-time ETL processing. Utilize Amazon SageMaker for model training, adjusting capacity with Spot Instances as needed. Store outcomes in DynamoDB for live recommendations.
- D) Direct clickstream data to Amazon S3 using Amazon Kinesis Data Firehose, conducting nightly analysis with AWS Glue DataBrew and Amazon SageMaker using On-Demand Instances for model training. Deploy results to DynamoDB for real-time recommendations.

**정답: A) Kinesis Data Firehose → S3 → SageMaker Spot Training → DynamoDB**

💡 추가 설명:

- **옵션 B (MSK + Aurora)** - 일일 배치 훈련에는 과도한 복잡성과 비용, 실시간 처리가 요구사항이 아님
- **옵션 C (Glue 실시간 ETL)** - 문제에서 ETL이 필요하다는 언급이 없으며, 불필요한 처리 단계 추가
- **옵션 D (DataBrew + On-Demand)** - DataBrew는 데이터 전처리가 명시되지 않은 상황에서 불필요, On-Demand는 비용 효율성 측면에서 불리