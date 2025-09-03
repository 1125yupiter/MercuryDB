---

title: data-lake-glue-athena-serverless
created: 2025-08-19
modified: 2025-08-19
tags:
- service/glue
- service/athena
- constraint/serverless
- usecase/data-lake
- deployment/minimal-maintenance
aliases: ["glue-athena", "data-transformation", "serverless-analytics"]

---

# 데이터 레이크 변환 및 분석을 위한 서버리스 솔루션

## 🎯 핵심 포인트

S3 데이터 레이크에서 구조화/비구조화 CSV 데이터를 변환하고 분석해야 하는 경우, AWS Glue와 Amazon Athena를 조합하여 최소한의 개발 노력과 운영 비용으로 서버리스 ETL 및 쿼리 환경을 구축할 수 있다.

## 📝 설명

### AWS Glue + Amazon Athena가 데이터 레이크 운영에 적합한 이유

**AWS Glue**는 서버리스 데이터 통합 서비스로, S3에 저장된 CSV 파일과 같은 반구조화/비구조화 데이터의 자동 스키마 발견, 변환, 정리 작업을 처리합니다. 크롤러를 통해 데이터 구조를 자동으로 파악하고 Data Catalog를 생성하며, ETL 작업의 자동 스케일링으로 인프라 관리 부담을 제거합니다.

**Amazon Athena**는 S3 데이터를 직접 쿼리할 수 있는 서버리스 대화형 쿼리 서비스입니다. 표준 SQL을 사용하여 데이터 레이크의 데이터를 분석할 수 있으며, 별도의 데이터 로딩이나 인프라 설정 없이 즉시 사용 가능합니다. 쿼리한 데이터양에 대해서만 비용을 지불하는 pay-per-query 모델을 채택합니다.

### 아키텍처 플로우

```
Raw CSV Data (S3) → AWS Glue Crawler → Data Catalog
                ↓
        AWS Glue ETL Jobs → Transformed Data (S3)
                ↓
        Amazon Athena ← Analysts (SQL Queries)
```

### Trade-offs 고려사항

**AWS Glue + Athena 장점**:
- 서버리스 아키텍처로 인프라 관리 불필요
- 사용량 기반 과금으로 비용 효율성
- 자동 스키마 발견 및 데이터 카탈로그 관리
- 표준 SQL 지원으로 기존 분석가들이 쉽게 적응
- 자동 스케일링으로 데이터량 변화에 유연 대응

**AWS Glue + Athena 단점**:
- 대용량 테이블 간 복잡한 조인에서 성능 제한 및 실행 시간 제한(30분)
- 실시간 처리에는 부적합 (배치 중심)
- 콜드 스타트로 인한 초기 지연 시간

**Amazon Redshift 장점**:
- 복합 분석 쿼리에 뛰어난 성능
- 고급 분석 기능 지원 (윈도우 함수 등)
- 일관된 쿼리 성능

**Amazon Redshift 단점**:
- 클러스터 관리 및 지속적인 비용 발생
- 데이터 로딩 프로세스 필요
- 스케일링 시 다운타임 발생

## 🔍 주요개념

### 서버리스 vs 관리형 서비스 비교

- **서버리스 (Glue/Athena)**: 사용량 기반 과금, 자동 스케일링, 인프라 관리 불필요, 개발 복잡도 최소화
- **관리형 서비스 (Redshift/EMR)**: 지속적인 리소스 할당, 성능 최적화 가능, 복잡한 워크로드 처리 우수

### 실전 적용

- **정기 보고서 생성**: 매월/분기별 비즈니스 메트릭 분석을 위한 ETL 파이프라인 구축
- **애드혹 분석**: 분석가들이 필요에 따라 데이터 레이크의 다양한 데이터셋을 탐색적으로 분석
- **데이터 품질 모니터링**: 들어오는 CSV 파일의 스키마 변화 감지 및 데이터 검증 자동화

## 📝 관련 문제

**Question:** An organization operates an extensive data lake on Amazon S3, comprising both structured and unstructured CSV files. As data arrives, it necessitates transformation and cleansing before being subjected to SQL query analysis by the company's analysts. Which approach minimizes development effort, costs, and maintenance requirements?

**Options:**

- A) Employ AWS Glue for data transformation, and utilize Amazon Redshift for querying the data
- B) Employ AWS Glue for the organization and transformation of unstructured data, and utilize Amazon Athena for querying the data
- C) Periodically transform the data using Amazon SageMaker, and conduct queries with Amazon Redshift
- D) Utilize Apache Spark on Amazon EMR for data transformation, and query the data using Amazon RDS

**정답: B) AWS Glue + Amazon Athena**

💡 추가 설명:

- **Option A (Glue + Redshift)** - Redshift 클러스터 운영 비용과 데이터 로딩 복잡도로 인해 유지관리 부담 증가
- **Option C (SageMaker + Redshift)** - SageMaker는 ML 중심 서비스로 단순 데이터 변환에는 과도하며 개발 복잡도 상승
- **Option D (EMR + RDS)** - EMR 클러스터 관리 필요, RDS는 대용량 분석 쿼리에 부적합한 관계형 DB 서비스