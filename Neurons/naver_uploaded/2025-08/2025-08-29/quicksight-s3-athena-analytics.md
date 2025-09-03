---

title: quicksight-s3-athena-analytics
created: 2025-08-20
modified: 2025-08-20
tags:
- service/quicksight
- service/athena
- service/sagemaker
- constraint/file-based
- usecase/sales-analytics
aliases: ["quicksight-s3", "athena-ml", "s3-visualization"]

---

# QuickSight와 S3 데이터 분석을 위한 Athena 연동 아키텍처

## 🎯 핵심 포인트

S3에 저장된 파일 기반 데이터를 QuickSight로 분석하는 경우 Athena를 통해 SQL 쿼리 기능을 제공하고, 고급 ML 분석을 위해 SageMaker를 연동할 수 있다.

## 📝 설명

### QuickSight가 S3 데이터 분석에 Athena가 필요한 이유

QuickSight는 파일 기반 데이터 소스(S3)에서는 SQL 쿼리를 직접 실행할 수 없다. 파일은 구조화된 데이터베이스가 아니므로 SQL 엔진이 별도로 필요하며, Athena가 이 역할을 담당한다. Athena는 S3의 파일들을 마치 데이터베이스 테이블처럼 취급하여 SQL 쿼리를 가능하게 한다.

### 아키텍처 플로우

```
S3 파일 데이터 (CSV, JSON, Parquet)
    ↓
AWS Glue Catalog (메타데이터 스키마)
    ↓
Amazon Athena (SQL 엔진)
    ↓
Amazon SageMaker (고급 ML 분석)
    ↓
Amazon QuickSight (시각화 및 대시보드)
```

### QuickSight의 데이터 연결 방식 비교

**파일 기반 데이터 소스**:
- S3, CSV, Excel 파일 등
- SQL 쿼리 불가능
- 단순 필터링, 집계 기능만 지원
- 복잡한 데이터 변환 작업 제한

**데이터베이스 기반 데이터 소스**:
- RDS, Redshift, Aurora 등
- Custom SQL 쿼리 직접 실행 가능
- 복잡한 JOIN, 서브쿼리 지원
- 실시간 데이터 처리 가능

### Trade-offs 고려사항

**Athena + SageMaker + QuickSight 조합 장점**:
- S3 데이터를 직접 쿼리하여 데이터 이동 최소화
- 서버리스 아키텍처로 인프라 관리 불필요
- 쿼리한 데이터양만큼 과금되어 비용 효율적
- SageMaker를 통한 고급 ML 알고리즘 활용 가능

**Athena + SageMaker + QuickSight 조합 단점**:
- 여러 서비스 연동으로 인한 복잡성 증가
- 실시간 데이터 처리에는 제한적

**QuickSight 내장 ML만 사용 시 장점**:
- 단순한 아키텍처
- 빠른 구현 가능

**QuickSight 내장 ML만 사용 시 단점**:
- 이상 탐지와 예측 기능만 제공
- 고객 행동 분석 등 복잡한 ML 분석 불가능

**Glue + Redshift + SageMaker 조합 장점**:
- Redshift ML을 통한 통합 ML 환경
- 대용량 데이터 웨어하우스 기능

**Glue + Redshift + SageMaker 조합 단점**:
- 불필요한 ETL 과정과 데이터 이동
- Redshift 클러스터 운영 비용 발생
- 데이터가 이미 S3에 있는 상황에서 비효율적

## 🔍 주요개념

### QuickSight ML 기능과 한계

- **Anomaly Detection**: 시계열 데이터의 이상 패턴 탐지
- **Forecasting**: 미래 값 예측 (시계열 분석)
- **Auto-narratives**: 자동 인사이트 생성
- **한계**: 클러스터링, 분류, 추천 시스템 등 고급 ML 분석 불가

### 파일 기반 vs 데이터베이스 기반 연결

- **파일 기반**: SQL 엔진 없음, 단순 데이터 처리만 가능
- **데이터베이스 기반**: 이미 SQL 엔진 보유, 복잡한 쿼리 실행 가능

### 실전 적용

- S3에 저장된 고객 구매 데이터를 Athena로 쿼리하여 월별 매출 트렌드 분석
- SageMaker로 고객 세그멘테이션 모델을 구축하고 QuickSight로 세그먼트별 대시보드 생성
- Athena를 통해 다중 테이블 JOIN 후 QuickSight에서 실시간 KPI 모니터링

## 📝 관련 문제

**Question:** A company wants to use Amazon QuickSight to analyze and visualize their sales data stored in Amazon S3. They want to apply machine learning algorithms to generate insights about customer behavior. Which option describes the best approach to achieve this?

**Options:**

- A) Use Amazon QuickSight's built-in machine learning capabilities to analyze the data in S3 and generate insights. Connect QuickSight to S3 to visualize the data.
- B) Use Amazon Athena to query the data in S3 and generate a table with the necessary columns. Use Amazon SageMaker to apply machine learning algorithms to the data and generate insights. Connect QuickSight to Athena to visualize the data.
- C) Use AWS Lambda to process the data in S3 and transform it into a format suitable for machine learning. Use Amazon SageMaker to apply machine learning algorithms and connect QuickSight to S3 to visualize the data.
- D) Use AWS Glue to crawl and catalog the data in S3, then create a Glue ETL job to transform and load the data into Amazon Redshift. Use Amazon SageMaker for ML algorithms and connect QuickSight to Redshift to visualize the data.

**정답: B) Athena + SageMaker + QuickSight 조합**

💡 추가 설명:

- **Option A** - QuickSight 내장 ML은 이상 탐지와 예측만 지원하여 복잡한 고객 행동 분석에 부적합
- **Option C** - Lambda를 통한 데이터 전처리는 불필요한 복잡성과 비용을 추가하며 비효율적
- **Option D** - 데이터가 이미 S3에 있는 상황에서 Redshift로 ETL하는 것은 불필요한 데이터 이동과 비용 발생