---

title: glue-etl-datalake-automation
created: 2025-08-18
modified: 2025-08-18
tags:
- service/glue
- constraint/minimal-effort
- deployment/serverless
- usecase/etl-automation
- architecture/datalake
aliases: ["glue-catalog", "etl-automation", "datalake-etl"]

---

# AWS Glue를 활용한 데이터레이크 ETL 자동화

## 🎯 핵심 포인트

최소한의 설정 및 유지보수 노력으로 S3 데이터레이크의 ETL 자동화가 필요한 경우, AWS Glue Data Catalog과 AWS Glue ETL 작업을 조합하여 완전 관리형 솔루션을 구축할 수 있다.

## 📝 설명

### AWS Glue가 데이터레이크 ETL 자동화에 적합한 이유

AWS Glue는 완전 관리형 ETL 서비스로서 서버 프로비저닝, 패치, 확장 등의 운영 부담을 AWS가 자동으로 처리합니다. AWS Glue Data Catalog은 데이터의 위치, 스키마, 런타임 메트릭을 저장하는 중앙화된 메타데이터 저장소 역할을 하며, S3의 데이터를 자동으로 크롤링하여 스키마를 발견할 수 있습니다. 이러한 통합된 아키텍처는 데이터 웨어하우스나 데이터 레이크 구축에 필수적인 메타데이터 관리와 ETL 작업을 하나의 서비스에서 제공합니다.

### 아키텍처 플로우

```
S3 Raw Data → AWS Glue Crawler → Glue Data Catalog
                                       ↓
Training Dataset ← S3 Processed ← Glue ETL Job
                                       ↑
                              Schedule/Trigger
```

### Trade-offs 고려사항

**AWS Glue 장점**:

- 완전 관리형 서비스로 인프라 관리 불필요
- 자동 스케일링과 서버리스 아키텍처
- 통합된 데이터 카탈로그와 ETL 작업 환경
- 사용한 만큼만 비용 지불하는 유연한 과금 모델
- Apache Spark 기반의 강력한 데이터 처리 능력

**AWS Glue 단점**:

- 복잡한 변환 로직의 경우 코딩 스킬 필요
- 실시간 스트리밍 처리에는 제한적
- 콜드 스타트로 인한 초기 지연 시간

**EMR 기반 솔루션 장점**:

- 더 세밀한 클러스터 제어 가능
- 다양한 오픈소스 도구 선택의 자유
- 대규모 배치 처리에 최적화

**EMR 기반 솔루션 단점**:

- 클러스터 설정, 구성, 관리의 운영 복잡성
- 24/7 클러스터 운영 시 높은 비용
- 수동적인 스케일링과 패치 관리 필요

## 🔍 주요개념

### 관리형 vs 비관리형 서비스 비교

- **완전 관리형(AWS Glue)**: AWS가 인프라, 패치, 스케일링 등 모든 운영을 담당하며 사용자는 비즈니스 로직에만 집중
- **부분 관리형(EMR)**: 기본 인프라는 AWS가 관리하지만 클러스터 설정과 애플리케이션 관리는 사용자 책임

### 실전 적용

- 일일 배치 ETL 작업으로 원시 데이터를 정제된 훈련 데이터셋으로 변환
- 다양한 소스에서 수집된 데이터를 표준화된 포맷으로 통합
- 머신러닝 파이프라인을 위한 피처 엔지니어링 자동화

## 📝 관련 문제

**Question:** A company has just created an Amazon S3 data lake for its existing and future training data. The Machine Learning Specialist at this company wants to design an automated solution that will handle all transformation jobs while keeping a catalog of metadata associated with the training dataset. The Specialist is looking for a solution that involves the least amount of effort to set up and maintain. Which solution should the Specialist use?

**Options:**

- A) Launch a transient EMR cluster and install Apache Hive. Use a Hive metastore for storing metadata and write a scheduled transformation job in a script.
- B) Use the AWS Glue Data Catalog to store metadata and create a custom AWS Glue ETL job to run data transformation jobs.
- C) Launch a transient EMR cluster and install Apache Spark. Schedule a Spark job to run data transformations and use AWS Glue Data Catalog for storing metadata.
- D) Use AWS Data Pipeline to automate data transformation jobs and use AWS Glue Data Catalog for storing metadata.

**정답: B) Use the AWS Glue Data Catalog to store metadata and create a custom AWS Glue ETL job to run data transformation jobs.**

💡 추가 설명:

- **Option A** - EMR 클러스터 생성 및 Hive 설치는 AWS Glue보다 더 많은 설정과 운영 노력이 필요
- **Option C** - EMR + Spark 조합은 클러스터 관리와 스케줄링을 위한 추가적인 구성 작업 필요
- **Option D** - AWS Data Pipeline은 여전히 EMR 등의 컴퓨팅 리소스 관리가 필요하여 운영 복잡성 증가

---