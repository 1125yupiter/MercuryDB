---

title: aws-lambda-kinesis-api-ingestion
created: 2025-08-19
modified: 2025-08-19
tags:
- service/lambda
- service/kinesis-firehose
- constraint/real-time
- usecase/data-ingestion
- architecture/serverless
aliases: ["lambda-api", "kinesis-ingestion", "serverless-etl"]

---

# AWS Lambda와 Kinesis를 활용한 Third-party API 데이터 수집 아키텍처

## 🎯 핵심 포인트

대용량 제3자 API 데이터를 AWS 환경으로 수집해야 하는 경우 AWS Lambda와 Amazon Kinesis Data Firehose에서, 서버리스 기반의 실시간 데이터 파이프라인을 구축할 수 있다.

## 📝 설명

### AWS Lambda가 API 데이터 수집에 적합한 이유

AWS Lambda는 이벤트 기반 서버리스 컴퓨팅 서비스로, 외부 API 호출 및 데이터 처리에 최적화되어 있다. 스케줄링된 트리거(CloudWatch Events)나 HTTP 요청을 통해 제3자 API를 주기적으로 호출하고, 응답 데이터를 실시간으로 변환하여 S3, DynamoDB, RDS 등 다양한 AWS 스토리지 서비스에 저장할 수 있다. 사용한 만큼만 과금되는 구조로 비용 효율적이며, 자동 확장 기능을 통해 데이터 볼륨 변화에 탄력적으로 대응한다.

### Amazon Kinesis Data Firehose가 스트리밍 데이터 처리에 효과적인 이유

Kinesis Data Firehose는 완전 관리형 실시간 데이터 전송 서비스로, 높은 처리량의 스트리밍 데이터를 안정적으로 수집하고 변환하여 최종 목적지로 전달한다. API에서 수집된 데이터를 실시간으로 배치 처리하여 S3, Redshift, Elasticsearch Service로 자동 전송하며, 데이터 변환, 압축, 암호화 기능을 내장하고 있어 별도의 ETL 프로세스 없이도 분석 준비가 완료된 데이터를 제공한다.

### 아키텍처 플로우

```
Third-party API → CloudWatch Events → AWS Lambda → Kinesis Data Firehose → S3/Redshift/ES
                      ↑                    ↓
                  Schedule/Trigger    Data Processing
                                          ↓
                                   Error Handling → DLQ (SQS)
```

### Trade-offs 고려사항

**AWS Lambda 장점**:
- 서버리스로 인프라 관리 불필요
- 자동 확장 및 고가용성 제공
- 이벤트 기반 실행으로 비용 효율적
- 다양한 프로그래밍 언어 지원

**AWS Lambda 단점**:
- 15분 실행 시간 제한
- 동시 실행 한계 (기본 1,000개)
- 콜드 스타트 지연 가능성

**Amazon Kinesis Data Firehose 장점**:
- 완전 관리형 실시간 데이터 전송
- 자동 확장 및 내장 변환 기능
- 백프레셔 핸들링 및 재시도 메커니즘
- 다양한 목적지 지원

**Amazon Kinesis Data Firehose 단점**:
- 최소 60초 배치 간격 (Near real-time)
- 레코드 크기 제한 (최대 1MB)
- 복잡한 데이터 변환 시 추가 Lambda 필요

**AWS Glue 단점**:
- 배치 처리 중심으로 실시간 API 호출에 부적합
- 스케줄링 기반으로 즉시성 부족
- API 연결을 위한 추가 설정 복잡

**Amazon SNS/SQS 단점**:
- 외부 데이터 수집 기능 없음
- 메시지 전달/큐잉 서비스로 데이터 처리 불가
- API 호출 및 변환 로직 별도 구현 필요

## 🔍 주요개념

### 서비스 특성 비교

- **AWS Lambda**: 이벤트 기반 코드 실행 서비스로 API 호출 및 데이터 가공 담당
- **Kinesis Data Firehose**: 스트리밍 데이터 전송 서비스로 대용량 데이터 배치 처리 및 전달
- **AWS Glue**: ETL 작업을 위한 서비스로 이미 저장된 데이터의 변환 및 카탈로그 관리

### 실전 적용

- **금융 데이터 수집**: 주식 가격 API를 Lambda로 주기적 호출 → Firehose를 통해 S3에 저장 → 실시간 대시보드 구성
- **소셜미디어 분석**: Twitter API 데이터를 Lambda로 수집 → Firehose에서 감정 분석 변환 → Elasticsearch로 전송하여 검색 가능
- **IoT 센서 데이터**: 제3자 IoT 플랫폼 API를 통해 센서 데이터 수집 → Firehose로 배치 처리 → Redshift에서 시계열 분석

## 📝 관련 문제

**Question:** A company needs to collect large volumes of data from a third-party weather API every 5 minutes and store it in Amazon S3 for analytics. The solution should be cost-effective and handle varying data loads automatically. Which combination of AWS services would be most appropriate?

**Options:**

- A) AWS Glue + Amazon SNS
- B) AWS Lambda + Amazon Kinesis Data Firehose
- C) Amazon EC2 + Amazon SQS
- D) AWS Batch + Amazon SNS
- E) Amazon EMR + Amazon SQS

**정답: B) AWS Lambda + Amazon Kinesis Data Firehose**

💡 추가 설명:

- **A) AWS Glue + Amazon SNS** - Glue는 배치 ETL 서비스로 실시간 API 호출에 부적합하며, SNS는 데이터 수집 기능이 없음
- **C) Amazon EC2 + Amazon SQS** - EC2는 서버 관리가 필요하고 비용 효율성이 떨어지며, SQS는 외부 데이터 수집 불가
- **D) AWS Batch + Amazon SNS** - Batch는 대용량 배치 작업용으로 5분 간격 호출에 과도하며, SNS는 데이터 처리 불가
- **E) Amazon EMR + Amazon SQS** - EMR은 빅데이터 처리 클러스터로 간단한 API 호출에는 과도한 솔루션이며, SQS는 데이터 수집 기능 없음