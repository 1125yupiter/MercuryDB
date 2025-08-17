---

title: etl-glue-minimal-operations
created: 2025-08-17
modified: 2025-08-17
tags:
- service/glue
- constraint/minimal-operations
- problem/etl-pipeline
- usecase/ml-training-data
- comparison/lambda-vs-glue
aliases: ["glue-etl", "serverless-etl", "managed-etl"]

---

# ETL 파이프라인 - 최소 운영 관리를 위한 AWS Glue 활용

## 🎯 핵심 포인트

운영 관리를 최소화해야 하는 경우 EMR + Step Functions 대신 AWS Glue ETL 작업을 사용하여 S3로 결과를 저장할 수 있다.

## 📝 설명

### AWS Glue가 최소 운영 관리에 적합한 이유

AWS Glue는 완전 관리형 서버리스 ETL 서비스로, 인프라 프로비저닝이나 관리 없이 데이터 변환 작업을 수행할 수 있습니다. 특히 ML 훈련 데이터 처리와 같은 논크리티컬 워크로드에서 운영 부담을 크게 줄여줍니다.

**핵심 특징:**
- **서버리스 아키텍처**: 클러스터 관리 불필요
- **자동 스케일링**: 워크로드에 따른 자동 리소스 조정
- **사용량 기반 과금**: 실행 시간만큼만 비용 지불
- **시각적 ETL 편집기**: 코드 작성 없이 파이프라인 구성 가능
- **자동 코드 생성**: 스키마 기반 PySpark 코드 자동 생성

### 아키텍처 플로우

```
Custom Data Sources
        ↓
    AWS Glue
  (ETL Job/Crawler)
        ↓
   Data Catalog
   (Schema Registry)
        ↓
  Transformation
  (Apache Spark)
        ↓
    Amazon S3
  (Processed Data)
        ↓
   ML Training
```

### Trade-offs 고려사항

**AWS Glue 장점**:
- 완전 관리형 서비스로 운영 부담 최소화
- 자동 스키마 발견 및 데이터 카탈로그 생성
- Apache Spark 기반으로 대용량 데이터 처리 최적화
- 다양한 데이터 소스 및 포맷 지원
- 시각적 워크플로우 편집기 제공

**AWS Glue 단점**:
- Cold start 지연시간 존재
- 실시간 처리에는 부적합
- 커스터마이징에 제한이 있을 수 있음

**Lambda 장점**:
- 빠른 시작 시간 (Warm start시)
- 완전한 코드 제어 가능
- 다양한 런타임 지원

**Lambda 단점**:
- **15분 실행 시간 제한**: ETL 작업에 부적합
- **모든 ETL 로직을 직접 구현** 필요
- 대용량 데이터 처리에 메모리 제약
- 복잡한 의존성 관리 및 배포 패키지 관리

## 🔍 주요개념

### 개발 복잡성 비교

- **AWS Glue**: 자동 코드 생성, 내장 커넥터, 시각적 편집기로 개발 시간 단축
- **Lambda**: 모든 ETL 로직, 에러 핸들링, 데이터 커넥터를 직접 구현해야 함

### 실전 적용

- **ML 데이터 파이프라인**: 다양한 소스에서 훈련 데이터 수집 및 전처리
- **데이터 레이크 구축**: S3 기반 데이터 레이크로 원시 데이터 변환
- **배치 데이터 처리**: 정기적인 대용량 데이터 변환 작업

## 📝 관련 문제

**Question:** A Data Engineering team is using a combination of Amazon EMR and AWS Step Functions for its transformation pipeline. The team is looking for an alternative that requires less operational management. The new pipeline will support non-critical workloads with ML training data coming from custom sources. Which method is the most effective for building the solution?

**Options:**

- A) Create an ETL job using AWS Glue to transform data and deliver the results into an Amazon S3 bucket
- B) Create a Lambda function to transform data and deliver the results into an Amazon S3 bucket
- C) Create an ETL job using AWS Glue to transform data and deliver the results into an Amazon Redshift cluster
- D) Create a Lambda function to transform data and deliver the results into an Amazon Redshift cluster

**정답: A) Create an ETL job using AWS Glue to transform data and deliver the results into an Amazon S3 bucket**

💡 추가 설명:

- **Option B (Lambda + S3)** - 15분 실행 시간 제한으로 ETL 작업에 부적합하며, 모든 ETL 로직을 직접 구현해야 하는 개발 부담
- **Option C (Glue + Redshift)** - Redshift는 단순 데이터 저장용으로는 비용이 높고 더 많은 운영 관리 필요
- **Option D (Lambda + Redshift)** - Lambda의 실행 시간 제한과 Redshift의 높은 운영 부담이 모두 존재