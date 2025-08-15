--- 
tags: 
  - service/aws-glue 
  - architecture/serverless-etl 
  - engine/apache-spark 
  - integration/multi-source 
  - usecase/data-warehouse-etl 
---
# AWS Glue - 서버리스 ETL 서비스와 다양한 데이터 소스 통합
## 🎯 핵심 포인트
다양한 데이터 소스에서 데이터를 추출하고 변환해야 하는 경우 AWS Glue에서, 서버리스 환경으로 자동 ETL 코드 생성과 Apache Spark 기반 처리를 할 수 있다.

## 📝 설명

### AWS Glue가 ETL 워크로드에 적합한 이유
AWS Glue는 완전 관리형 서버리스 ETL 서비스로, 데이터 준비와 로드 작업을 자동화합니다. 핵심 특징은 다음과 같습니다:

- **자동 ETL 코드 생성**: 데이터 소스를 분석해 Python/Scala 코드를 자동 생성
- **다양한 데이터 소스 지원**: S3, JDBC 데이터베이스(Oracle, MySQL, PostgreSQL 등), RDS, Aurora, Redshift 등
- **메타데이터 관리**: Data Catalog을 통한 스키마 추론 및 테이블 자동 생성
- **Apache Spark 엔진**: 분산 처리를 통한 대용량 데이터 효율적 처리
- **서버리스 아키텍처**: 인프라 관리 없이 사용량 기반 과금

### 아키텍처 플로우
```
[다양한 데이터 소스]
         ↓
    [Glue Crawler] → [Data Catalog] (메타데이터 저장)
         ↓
   [Glue ETL Job] → [Apache Spark Engine] (코드 실행)
         ↓
   [Target Store] (S3, Redshift, RDS 등)
```

### Trade-offs 고려사항
**AWS Glue 장점**: 
- 서버리스로 인프라 관리 불필요
- 자동 코드 생성으로 개발 시간 단축
- 다양한 데이터 소스 지원 (온프레미스 포함)
- Apache Spark의 분산 처리 성능

**AWS Glue 단점**: 
- Spark 기반으로 소규모 데이터엔 오버헤드
- 실시간 스트리밍보다는 배치 처리에 최적화
- 복잡한 변환 로직은 커스터마이징 필요

**AWS Lambda 장점**: 
- 빠른 시작 시간, 소규모 데이터 처리에 효율적

**AWS Lambda 단점**: 
- 15분 실행 시간 제한으로 대용량 ETL 부적합
- 메모리 제한(최대 10GB)으로 복잡한 변환 작업 한계

## 🔍 주요개념

### 핵심 구성 요소 비교
- **Glue Crawler**: 데이터 소스를 스캔해 스키마를 자동으로 추론하고 Data Catalog에 테이블 생성
- **Glue ETL Job**: Apache Spark 기반으로 실제 데이터 변환 작업을 수행하는 실행 단위
- **Data Catalog**: 메타데이터 저장소로 테이블 스키마, 파티션 정보 등을 중앙 관리
- **JDBC Connection**: 외부 데이터베이스 연결을 위한 설정 (온프레미스, 타 클라우드 포함)

### 실전 적용
- **하이브리드 클라우드 데이터 통합**: 온프레미스 Oracle DB → S3 → Redshift 파이프라인
- **멀티 소스 데이터 레이크 구축**: 다양한 JDBC 데이터베이스를 S3 중앙 저장소로 통합
- **데이터 웨어하우스 ETL**: RDS/Aurora 운영 데이터를 Redshift 분석 환경으로 변환 및 적재

## 📝 관련 문제

**Question:** A company needs to build an ETL pipeline that integrates data from on-premises MySQL and PostgreSQL databases with cloud-based Aurora and S3 data into a Redshift data warehouse. The solution should automatically infer schemas, generate ETL code, and handle large-scale data processing without server management. Which service best meets these requirements?

**Options:**
- A) AWS Lambda with custom Python scripts
- B) Amazon EMR with manual Spark job configuration
- C) AWS Glue with Data Catalog and ETL jobs
- D) AWS Data Pipeline with EC2 instances
- E) Amazon Kinesis Data Firehose

**정답: C) AWS Glue with Data Catalog and ETL jobs**

💡 추가 설명:
• **A) AWS Lambda** - 15분 실행 시간 제한과 메모리 제약으로 대용량 ETL에 부적합
• **B) Amazon EMR** - 수동 설정 필요하고 서버 관리가 필요해 요구사항에 맞지 않음
• **D) AWS Data Pipeline** - EC2 인스턴스 관리가 필요하고 자동 코드 생성 기능 없음
• **E) Amazon Kinesis Data Firehose** - 실시간 스트리밍 서비스로 배치 ETL 시나리오에 부적합

	## 🏷️ 태그
	---
	tags:
	 - service/aws-glue
	 - architecture/serverless-etl
	 - engine/apache-spark
	 - integration/multi-source
	 - usecase/data-warehouse-etl
	---