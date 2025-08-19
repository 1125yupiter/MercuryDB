---

title: aws-data-pipeline-redshift-etl
created: 2025-08-19
modified: 2025-08-19
tags:
- service/data-pipeline
- service/redshift
- usecase/multi-source-etl
- constraint/built-in-activity
- technique/redshiftcopyactivity
aliases: ["AWS Data Pipeline", "RedshiftCopyActivity", "Multi-source ETL"]

---

# AWS Data Pipeline을 활용한 다중 소스 데이터 웨어하우스 구축

## 🎯 핵심 포인트

여러 소스에서 데이터를 수집하여 Redshift 데이터 웨어하우스에 로딩해야 하는 경우 AWS Data Pipeline에서, RedshiftCopyActivity를 통해 커스텀 코드 없이 효과적으로 구축할 수 있다.

## 📝 설명

### AWS Data Pipeline이 다중 소스 ETL에 적합한 이유

AWS Data Pipeline은 분산된 컴퓨팅 리소스 간에 정기적으로 데이터를 이동하고 변환할 수 있는 웹 서비스입니다. 특히 RedshiftCopyActivity라는 내장 활동을 제공하여, Amazon S3, DynamoDB, RDS 등 다양한 데이터 소스에서 Amazon Redshift로 데이터를 효율적으로 적재할 수 있습니다.

RedshiftCopyActivity는 내부적으로 Redshift의 COPY 명령어를 사용하여 대량 데이터 로딩을 최적화하며, 병렬 처리를 통해 높은 처리량을 달성합니다. 또한 데이터 압축, 암호화, 오류 처리 등의 기능을 자동으로 지원합니다.

### 아키텍처 플로우

```
[Multiple Data Sources]
    ↓
    ├─ Amazon S3 ─────────┐
    ├─ DynamoDB ──────────┤
    ├─ RDS ───────────────┤ → [AWS Data Pipeline]
    ├─ On-premise DB ─────┤      ↓
    └─ Third-party APIs ──┘   [RedshiftCopyActivity]
                                 ↓
                            [Amazon Redshift]
                                 ↓
                            [Data Warehouse]
```

### Trade-offs 고려사항

**AWS Data Pipeline 장점**:
- RedshiftCopyActivity로 다중 소스 지원
- GUI 기반 시각적 파이프라인 설계
- 내장 활동으로 개발 시간 단축
- 자동 재시도 및 오류 처리
- 스케줄링과 의존성 관리 기능

**AWS Data Pipeline 단점**:
- 복잡한 데이터 변환 로직에는 제한적
- 실시간 스트리밍 데이터 처리 불가
- 레거시 서비스로 신규 기능 추가 제한적

**AWS Glue 장점**:
- 서버리스 아키텍처
- Apache Spark 기반 강력한 변환 기능
- 자동 스키마 검색 및 카탈로그 관리

**Amazon Kinesis Data Firehose 단점**:
- 실시간 스트리밍에 특화되어 배치 처리 부적합
- 복잡한 데이터 조인 및 변환 로직 구현 어려움

## 🔍 주요개념

### 핵심 컴포넌트 비교

- **RedshiftCopyActivity**: Redshift COPY 명령을 활용한 고성능 데이터 로딩 활동
- **Data Pipeline Definition**: JSON 형태의 파이프라인 구조 정의
- **Pipeline Objects**: 데이터 노드, 활동, 전제 조건 등의 구성 요소
- **EC2 Resources**: 파이프라인 실행을 위한 컴퓨팅 리소스

### 실전 적용

- 일일 배치로 여러 소스에서 판매 데이터를 수집하여 BI 대시보드용 데이터 마트 구축
- 월별 재무 데이터를 다양한 시스템에서 통합하여 CFO 보고서용 데이터 웨어하우스 생성
- 고객 행동 데이터를 웹사이트, 모바일 앱, CRM에서 수집하여 마케팅 분석 플랫폼 구축

## 📝 관련 문제

**Question:** As a data engineer, you are tasked with designing a data pipeline that ingests data from a variety of sources and processes it before loading it into an Amazon Redshift data warehouse. You want to use AWS Data Pipeline to accomplish this task. Which of the following statements is true regarding the use of AWS Data Pipeline for this scenario?

**Options:**

- A) AWS Data Pipeline does not support loading data into Amazon Redshift from multiple sources, so it cannot be used for this scenario.
- B) AWS Data Pipeline only supports loading data into Amazon Redshift from Amazon S3, so it cannot be used for this scenario.
- C) AWS Data Pipeline provides a built-in activity called RedshiftCopyActivity that can be used to load data from a variety of sources into Amazon Redshift.
- D) AWS Data Pipeline requires you to write custom code to load data into Amazon Redshift from multiple sources.
- E) AWS Data Pipeline can only be used to load data into Amazon Redshift using Amazon Kinesis Data Firehose.

**정답: C) AWS Data Pipeline provides a built-in activity called RedshiftCopyActivity that can be used to load data from a variety of sources into Amazon Redshift.**

💡 추가 설명:

- **Option A** - AWS Data Pipeline은 실제로 다중 소스를 지원하므로 부적합
- **Option B** - S3 외에도 DynamoDB, RDS 등 다양한 소스를 지원하므로 부정확
- **Option D** - RedshiftCopyActivity라는 내장 활동으로 커스텀 코드 없이 구현 가능
- **Option E** - Kinesis Data Firehose 없이도 RedshiftCopyActivity를 통해 직접 로딩 가능