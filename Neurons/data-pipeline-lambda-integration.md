---

title: data-pipeline-lambda-integration
created: 2025-08-20
modified: 2025-08-20
tags:
- service/data-pipeline
- service/lambda
- integration/workflow
- processing/batch
- architecture/etl
aliases: ["aws-data-pipeline", "lambda-integration", "etl-pipeline"]

---

# AWS Data Pipeline와 Lambda 함수 통합을 통한 ETL 워크플로우

## 🎯 핵심 포인트

RDS에서 데이터를 추출하여 처리 후 S3에 저장하는 배치 ETL 파이프라인을 구축하는 경우, AWS Data Pipeline에서 Lambda 함수를 워크플로우의 일부로 실행할 수 있다.

## 📝 설명

### AWS Data Pipeline이 ETL 워크플로우에 적합한 이유

AWS Data Pipeline은 배치 데이터 처리를 위해 설계된 서비스로, 다양한 AWS 서비스들을 연결하여 복잡한 데이터 처리 워크플로우를 구성할 수 있습니다. Lambda 함수를 파이프라인의 구성 요소로 통합하여 데이터 변환, 복잡한 계산, 비즈니스 로직 처리 등을 수행할 수 있어 확장 가능하고 유연한 데이터 처리 파이프라인을 구축할 수 있습니다.

### 아키텍처 플로우

```
Amazon RDS → AWS Data Pipeline → AWS Lambda → Amazon S3
     ↓              ↓                ↓            ↓
  원본 데이터    워크플로우 관리    데이터 처리    결과 저장
                스케줄링/모니터링   변환/계산     최종 데이터
```

### Trade-offs 고려사항

**AWS Data Pipeline 장점**:
- 시각적 인터페이스와 코드 기반 워크플로우 수정 모두 지원
- 다양한 데이터 소스 및 대상 지원 (RDS, S3, DynamoDB 등)
- Lambda 함수 통합을 통한 커스텀 처리 로직 구현
- 유연한 데이터 형식 지원 (CSV, JSON, Parquet, 커스텀 형식)
- 자동 스케줄링 및 오류 처리

**AWS Data Pipeline 단점**:
- 배치 처리 전용 (실시간 처리 불가)
- 복잡한 워크플로우 설정 시 학습 곡선
- 비용이 실행 시간에 따라 증가

**Amazon Kinesis 장점**:
- 실시간 스트리밍 데이터 처리
- 낮은 지연 시간

**Amazon Kinesis 단점**:
- 배치 ETL 작업에는 과도한 기능
- 복잡한 스케줄링 로직이 별도로 필요

## 🔍 주요개념

### 배치 vs 스트리밍 처리

- **배치 처리**: 정해진 시간 간격으로 대량의 데이터를 한 번에 처리하는 방식
- **스트리밍 처리**: 실시간으로 들어오는 데이터를 연속적으로 처리하는 방식

### 실전 적용

- 일일 판매 데이터를 RDS에서 추출하여 분석 후 S3에 보고서 저장
- 고객 행동 로그를 Lambda로 전처리하여 데이터 웨어하우스로 적재
- 정기적인 데이터베이스 백업 및 아카이빙 작업 자동화

## 📝 관련 문제

**Question:** As a data engineer, you need to design a pipeline that extracts data from an Amazon RDS instance, processes the data using an AWS Lambda function, and stores the results in an Amazon S3 bucket. Which of the following statements is true regarding AWS Data Pipeline?

**Options:**

- A) AWS Data Pipeline provides a graphical interface for creating data processing workflows, but you cannot modify the workflows using code
- B) AWS Data Pipeline supports real-time data processing and is ideal for use cases where you need to process streaming data
- C) AWS Data Pipeline allows you to execute AWS Lambda functions as part of your data processing workflow
- D) AWS Data Pipeline only supports data sources that are stored in Amazon S3
- E) AWS Data Pipeline requires you to define the input and output data formats explicitly, which can limit the flexibility of your data processing workflow

**정답: C) AWS Data Pipeline allows you to execute AWS Lambda functions as part of your data processing workflow**

💡 추가 설명:

- **A번** - 그래픽 인터페이스 제공과 동시에 코드를 통한 워크플로우 수정도 지원함
- **B번** - 배치 처리 서비스로 실시간 스트리밍 데이터 처리에는 부적합함
- **D번** - RDS, DynamoDB 등 다양한 데이터 소스를 지원함
- **E번** - CSV, JSON, Parquet 등 다양한 형식과 커스텀 형식까지 유연하게 지원함