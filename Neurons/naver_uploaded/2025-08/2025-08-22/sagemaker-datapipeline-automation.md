---

title: sagemaker-datapipeline-automation
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker
- service/datapipeline
- constraint/automation
- deployment/etl
- storage/s3
aliases: ["ML Data Pipeline", "SageMaker ETL", "automated-ml-workflow"]

---

# SageMaker를 위한 자동화된 데이터 파이프라인 구축

## 🎯 핵심 포인트

SQL Server 데이터를 SageMaker에서 머신러닝 모델 훈련에 사용하는 경우, AWS Data Pipeline을 통해 S3로 데이터를 이동하여 완전 자동화된 워크플로를 구축할 수 있다.

## 📝 설명

### AWS Data Pipeline이 ML 워크플로 자동화에 적합한 이유

AWS Data Pipeline은 다양한 데이터 소스 간의 데이터 이동과 변환을 자동화하는 웹 서비스입니다. SageMaker는 데이터 소스로 Amazon S3를 요구하므로, SQL Server에서 S3로의 데이터 이동이 필요합니다. Data Pipeline은 정기적인 ETL 작업을 스케줄링하고, 데이터 품질 검증, 실패 처리, 재시도 로직을 포함한 견고한 파이프라인을 구축할 수 있습니다.

### 아키텍처 플로우

```
SQL Server (RDS) 
    ↓ (Extract)
AWS Data Pipeline
    ↓ (Transform & Load)
Amazon S3 Bucket
    ↓ (Training Data)
SageMaker Training Job
    ↓ (Model Output)
S3 Model Artifacts
    ↓ (Deployment)
SageMaker Endpoints
```

### Trade-offs 고려사항

**AWS Data Pipeline 장점**:
- 완전 관리형 서비스로 인프라 관리 불필요
- 스케줄링 및 의존성 관리 자동화
- 다양한 데이터 소스와 대상 지원
- 실패 처리 및 재시도 로직 내장
- 비용 효율적인 온디맨드 처리

**AWS Data Pipeline 단점**:
- 복잡한 변환 로직에 대한 제한
- 실시간 처리보다는 배치 처리에 최적화
- 설정 초기 복잡성

**직접 연결 방식 장점**:
- 실시간 데이터 접근 가능
- 중간 저장소 없이 직접 처리

**직접 연결 방식 단점**:
- SageMaker에서 지원하지 않음
- 네트워크 의존성 높음
- 데이터베이스 부하 증가

## 🔍 주요개념

### ETL vs ELT 비교

- **ETL (Extract-Transform-Load)**: 데이터를 추출하고 변환한 후 대상에 로드. Data Pipeline의 기본 방식
- **ELT (Extract-Load-Transform)**: 원본 데이터를 먼저 로드한 후 대상에서 변환. 대용량 데이터 처리에 효율적

### 실전 적용

- **금융 사기 탐지**: 거래 데이터를 매일 밤 SQL Server에서 S3로 이동하여 이상 탐지 모델 재훈련
- **고객 이탈 예측**: CRM 데이터를 주간 단위로 추출하여 고객 행동 패턴 분석 모델 업데이트  
- **재고 수요 예측**: ERP 시스템 데이터를 실시간으로 수집하여 계절성 예측 모델 지속적 개선

## 📝 관련 문제

**Question:** A Machine Learning Specialist was granted access to a SQL Server hosted on Amazon RDS, whose data will be used to train a model in Amazon SageMaker. The Specialist intends to design an end-to-end solution to automate his workflow. How can the Specialist implement the solution?

**Options:**

- A) Use AWS DMS to migrate the SQL Server data to an Amazon OpenSearch cluster for quick access. Provide the cluster's endpoint within the SageMaker notebook.
- B) Directly pull the data from the SQL database by establishing a connection within the Amazon SageMaker instance.
- C) Export the Microsoft SQL Server data to Amazon DynamoDB. Provide the source DynamoDB table name within the SageMaker notebook.
- D) Use AWS Data Pipeline to copy the data from SQL Server to an S3 bucket. Provide the S3 endpoint within the SageMaker notebook.

**정답: D) AWS Data Pipeline을 사용하여 SQL Server에서 S3 버킷으로 데이터 복사**

💡 추가 설명:

- **옵션 A (AWS DMS + OpenSearch)** - OpenSearch는 검색/분석용으로 SageMaker와 직접 연동되지 않으며, ML 훈련에는 S3가 필요
- **옵션 B (직접 SQL 연결)** - SageMaker는 RDS SQL Server와 직접 연결을 지원하지 않음. 반드시 S3를 통한 데이터 제공 필요
- **옵션 C (DynamoDB 사용)** - DynamoDB도 SageMaker와 직접 연결 불가하며, 관계형 데이터를 NoSQL로 변환하는 것은 불필요한 복잡성 추가