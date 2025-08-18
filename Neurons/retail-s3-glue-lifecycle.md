---

title: retail-s3-glue-lifecycle
created: 2025-08-18
modified: 2025-08-18
tags:
- service/s3
- service/glue
- constraint/cost-effective
- usecase/retail-analytics
- technique/partitioning
aliases: ["retail-data-architecture", "s3-lifecycle", "data-archiving"]

---

# 리테일 데이터 분석을 위한 S3 기반 아키텍처

## 🎯 핵심 포인트

대용량 리테일 판매 데이터를 일일 분석하고 90일 후 아카이빙해야 하는 경우, S3 날짜 기반 파티셔닝과 AWS Glue, 그리고 S3 lifecycle 정책을 조합하여 빠른 쿼리와 최소 비용의 아카이빙을 동시에 달성할 수 있다.

## 📝 설명

### S3 + Glue + Lifecycle이 리테일 데이터 분석에 적합한 이유

이 아키텍처는 리테일 체인의 일일 판매 데이터를 효율적으로 처리하기 위한 최적의 조합입니다. S3의 날짜별 프리픽스 구조는 파티션 프루닝을 통해 30일 분석 시 필요한 데이터만 스캔하도록 하며, AWS Glue Catalog의 파티셔닝은 쿼리 성능을 극대화합니다. S3 lifecycle 정책은 수동 개입 없이 자동으로 오래된 데이터를 저렴한 스토리지 클래스로 이동시켜 운영 비용을 최소화합니다.

### 아키텍처 플로우

```
리테일 아울렛들
    ↓ (일일 판매 데이터 업로드)
S3 버킷 (날짜별 프리픽스 구조)
├── sales-data/year=2024/month=08/day=18/
├── sales-data/year=2024/month=08/day=17/
└── ...
    ↓
AWS Glue Catalog
(날짜 기반 파티셔닝된 테이블 생성)
    ↓
분석 도구들 (Athena, EMR, Redshift Spectrum)
(30일 트렌드 분석을 위한 파티션 프루닝 쿼리)
    ↓
S3 Lifecycle 정책
(90일 후 자동으로 Glacier로 아카이빙)
```

### Trade-offs 고려사항

**S3 + Glue + Lifecycle 장점**:
- 단일 소스로 중복 저장 비용 없음
- 자동화된 아카이빙으로 운영 부담 최소
- 서버리스 아키텍처로 인프라 관리 불필요
- 파티션 프루닝으로 쿼리 성능 최적화
- 점진적 비용 감소 (Standard → IA → Glacier)

**S3 + Glue + Lifecycle 단점**:
- 초기 설정 시 파티션 구조 설계 필요
- Glue Crawler 실행 비용 (최소한)
- 아카이브된 데이터 복원 시 시간 소요

**Amazon Timestream 장점**:
- 시계열 데이터에 특화된 쿼리 성능
- 자동 데이터 압축 및 계층화
- 내장된 시계열 함수들

**Amazon Timestream 단점**:
- S3와 이중 저장으로 비용 증가
- 배치 로딩 프로세스 추가 복잡성
- 시계열 DB 저장 비용이 S3 대비 높음

**Redshift 장점**:
- 강력한 SQL 분석 기능
- 복잡한 조인 쿼리에 최적화
- BI 도구와의 뛰어난 호환성

**Redshift 단점**:
- 매일 데이터 복사로 ETL 비용 증가
- 클러스터 상시 운영 비용
- 테이블 관리 스크립트 필요

## 🔍 주요개념

### 스토리지 계층 비교

- **S3 Standard**: 자주 접근하는 데이터용, 높은 가용성과 내구성
- **S3 IA (Infrequent Access)**: 30일 이후 접근 빈도 낮은 데이터용
- **S3 Glacier**: 90일 이후 장기 보관용, 복원에 시간 필요
- **S3 Deep Archive**: 장기 백업용, 가장 저렴하지만 복원에 12시간 소요

### 실전 적용

- 패션 리테일 체인의 일일 매출, 재고, 고객 데이터 분석
- 식품 체인의 지역별 판매 트렌드 및 계절성 분석
- 온라인 커머스의 실시간 주문 데이터와 히스토리컬 데이터 관리

## 📝 관련 문제

**Question:** A prominent retail chain accumulates daily sales data dumps from each outlet into Amazon S3. The requirement is to execute daily analytical reports on sales trends spanning the previous 30 days. Furthermore, the data is to be archived post-90 days. Which architectural solution ensures rapid data querying and efficient archiving at minimal costs?

**Options:**

- A) Implement Amazon Timestream for scheduled batch loading of sales data from S3, organized with date-based prefixes, and configure Timestream's retention policies to automatically manage data older than 90 days.
- B) Consolidate all data into a singular partition, utilizing AWS Glue ETL to filter and eliminate data surpassing 90 days.
- C) Transfer the data nightly into Amazon Redshift, establishing a table for every day's data. Implement a nightly script to remove tables that exceed 90 days.
- D) Arrange the data within S3 using prefixes that denote the specific date covered by the data, utilize AWS Glue for the creation of tables partitioned by date, and apply S3 lifecycle policies for automatic archiving of data to Glacier after 90 days.

**정답: D) S3 날짜 기반 파티셔닝 + AWS Glue + S3 Lifecycle**

💡 추가 설명:

- **옵션 A (Timestream)** - S3와 Timestream 이중 저장으로 비용 증가, 배치 로딩 프로세스 복잡성 추가
- **옵션 B (단일 파티션)** - 전체 데이터 스캔 필요로 쿼리 성능 저하, 확장성 부족
- **옵션 C (Redshift)** - 매일 데이터 복사 및 클러스터 유지비용 과다, 테이블 관리 복잡성