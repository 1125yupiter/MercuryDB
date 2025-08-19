---

title: quicksight-dynamodb-realtime-dashboard
created: 2025-08-19
modified: 2025-08-19
tags:
- service/quicksight
- service/dynamodb
- constraint/real-time
- usecase/order-monitoring
- deployment/dashboard
aliases: ["quicksight-dynamo", "realtime-dashboard", "order-monitoring"]

---

# QuickSight와 DynamoDB를 활용한 실시간 주문 모니터링 대시보드

## 🎯 핵심 포인트

실시간 주문 상태 모니터링이 필요한 경우 DynamoDB에 저장된 데이터에서, QuickSight의 내장 커넥터를 사용하여 직접 연결하고 CloudWatch로 알람을 설정할 수 있다.

## 📝 설명

### QuickSight가 실시간 대시보드에 적합한 이유

QuickSight는 DynamoDB에 대한 네이티브 커넥터를 제공하여 데이터 이동이나 변환 과정 없이 직접 연결할 수 있다. 이는 실시간 모니터링에 필수적인 지연 시간을 최소화하고, 추가적인 데이터 저장소나 ETL 프로세스 없이도 최신 데이터를 시각화할 수 있게 한다. CloudWatch와의 통합을 통해 DynamoDB 메트릭 기반의 알람 설정이 가능하여 완전한 모니터링 솔루션을 구성할 수 있다.

### 아키텍처 플로우

```
온라인 주문 시스템 → DynamoDB (주문 데이터 저장)
                        ↓
                   QuickSight (직접 연결)
                        ↓
                   실시간 대시보드 생성
                        ↓
                   CloudWatch (메트릭 모니터링)
                        ↓
                   조건 기반 알람 발생
```

### Trade-offs 고려사항

**QuickSight 직접 연결 장점**:
- 추가 데이터 이동 없이 실시간 모니터링 가능
- 비용 효율적 (중간 저장소 불필요)
- 아키텍처 복잡도 최소화
- 네이티브 커넥터로 최적화된 성능

**QuickSight 직접 연결 단점**:
- 복잡한 데이터 변환이 필요한 경우 제한적
- DynamoDB 쿼리 패턴에 의존적
- 대용량 스캔 시 비용 증가 가능성

**Glue + Redshift 대안 장점**:
- 복잡한 분석 쿼리에 적합
- 데이터 웨어하우스 구조로 확장성

**Glue + Redshift 대안 단점**:
- 실시간성 저하 (배치 처리)
- 높은 비용과 복잡성
- 추가 인프라 관리 필요

## 🔍 주요개념

### 실시간 vs 배치 처리 비교

- **실시간 처리**: 데이터가 생성되는 즉시 처리하여 대시보드에 반영, 주문 상태 변화를 즉각 모니터링 가능
- **배치 처리**: 정해진 시간 간격으로 데이터를 처리, 지연 발생하지만 대용량 데이터 분석에 효율적

### 실전 적용

- EC 사이트에서 주문 처리 상태를 실시간으로 모니터링하여 지연되는 주문을 즉시 파악
- 배송 상태 변화를 실시간으로 추적하여 고객 문의 대응 시간 단축
- 재고 수준을 실시간으로 모니터링하여 품절 위험 상품을 사전에 식별

## 📝 관련 문제

**Question:** A company wants to build a real-time dashboard to monitor the status of their online orders. They have their data stored in Amazon DynamoDB. They want to use Amazon QuickSight to visualize the data and set up alerts based on certain conditions. Which of the following options describes the best way to achieve this?

**Options:**

- A) Use AWS Glue to crawl and catalog the data in DynamoDB, and then create a Glue ETL job to transform and load the data into Amazon Redshift. Connect QuickSight to Redshift and create the dashboard. Use Amazon SNS to set up alerts based on the data in Redshift.
- B) Use AWS Data Pipeline to copy the data from DynamoDB to Amazon Kinesis Data Firehose, and then transform and load the data into Amazon Elasticsearch. Connect QuickSight to Elasticsearch and create the dashboard. Use Amazon SNS to set up alerts based on the data in Elasticsearch.
- C) Use the built-in connectors in Amazon QuickSight to connect to DynamoDB and create a dashboard. Use Amazon CloudWatch to set up alerts based on the data in DynamoDB.
- D) Use AWS Lambda to query the data in DynamoDB, transform it, and load it into Amazon S3. Use Amazon Athena to query the data in S3 and create a dashboard in QuickSight. Use Amazon SNS to set up alerts based on the data in S3.

**정답: C) Use the built-in connectors in Amazon QuickSight to connect to DynamoDB and create a dashboard. Use Amazon CloudWatch to set up alerts based on the data in DynamoDB.**

💡 추가 설명:

- **Option A** - Glue ETL과 Redshift를 통한 데이터 처리는 실시간성을 저해하고 불필요한 복잡성과 비용을 추가
- **Option B** - Data Pipeline과 Elasticsearch를 사용하는 것은 과도하게 복잡한 아키텍처로 실시간 모니터링에 부적합
- **Option D** - Lambda와 S3, Athena를 통한 처리는 실시간 모니터링보다는 배치 분석에 적합하며 지연 발생