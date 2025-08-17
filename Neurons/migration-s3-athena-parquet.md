---
title: migration-s3-athena-parquet.md
created: 2025-08-17
modified: 2025-08-17
tags:
- service/s3
- service/athena
- service/glue
- technique/columnar-format
- constraint/query-performance
aliases: ["csv-to-parquet", "athena parquet optimization", "s3 parquet format"]
---

# CSV 데이터 마이그레이션 시 Athena 쿼리 최적화를 위한 Apache Parquet 변환

## 🎯 핵심 포인트

수백만 CSV 데이터를 S3로 마이그레이션한 경우, Athena 쿼리 성능 최적화를 위해 Apache Parquet 컬럼 기반 포맷으로 변환할 수 있다.

## 📝 설명

### Athena가 CSV 대신 Parquet에 적합한 이유
- CSV는 **row-based 포맷**이라 쿼리 시 전체 데이터를 스캔해야 하므로 비효율적임.  
- Parquet은 **columnar format**으로, 필요한 컬럼만 읽기 때문에 쿼리 성능이 크게 향상됨.  
- **Predicate Pushdown** 기능을 통해 불필요한 데이터 블록은 읽지 않고 건너뜀.  
- 평균적으로 CSV 대비 **2배 빠른 처리 속도, 6배 적은 스토리지 사용량**을 제공.  
- AWS Glue로 CSV → Parquet 변환 파이프라인을 구성 가능.

### 아키텍처 플로우

CSV 파일 (수백만 건) → Amazon S3 업로드
↓
AWS Glue ETL Job
↓ (CSV → Parquet 변환)
Parquet 파일 저장 (S3, 파티셔닝/압축)
↓
Amazon Athena / Redshift Spectrum 쿼리

### Trade-offs 고려사항

**Parquet 장점**:
- 컬럼 기반 저장으로 쿼리 성능 최적화
- 저장 비용 절감 (압축 효율 우수)
- Athena, Redshift Spectrum 등과 호환

**Parquet 단점**:
- 초기 변환 작업 비용 발생
- 실시간 데이터 삽입에는 부적합 (배치 전환에 적합)

**CSV (gzip) 장점**:
- 단순 저장 및 압축 효율 개선
- 파이프라인 간 호환성 높음

**CSV (gzip) 단점**:
- 여전히 row-based → 쿼리 시 전체 스캔
- 성능 및 비용 최적화에 불리

**JSON 장점**:
- 반정형 데이터 구조 지원
- 다양한 서비스와 호환성 높음

**JSON 단점**:
- 컬럼 기반 최적화 미지원
- 데이터 크기 증가로 비용 상승

## 🔍 주요개념

### 컬럼 기반 포맷 vs 행 기반 포맷

- **Row-based (CSV, JSON, XML)**  
  전체 행을 읽어야 하므로 특정 컬럼만 필요해도 불필요한 데이터가 스캔됨.

- **Columnar (Parquet, ORC)**  
  필요한 컬럼만 읽기 때문에 쿼리 효율이 높고, 대규모 분석에 최적.

### 실전 적용

- **로그 분석**: 수십억 행의 CSV 로그를 Parquet으로 변환 후 Athena 쿼리로 빠르게 분석  
- **BI 리포팅**: Redshift Spectrum에서 Parquet 데이터를 직접 조회하여 비용 절감  
- **데이터레이크 통합**: Glue ETL로 CSV → Parquet 변환 후 ML 학습/분석용 데이터세트 생성  

## 📝 관련 문제

**Question:**  
A Machine Learning Specialist is migrating hundreds of thousands of records in CSV files into an Amazon S3 bucket. Each file has 150 columns and is about 1 MB in size. Most of the queries will span a minimum of 5 columns. The data must be transformed to minimize the query runtime. Which transformation method will optimize query performance?

**Options:**
- A) Transform the files to JSON data format  
- B) Transform the files to gzip-compressed CSV data format  
- C) Transform the files to XML data format  
- D) Transform the files to Apache Parquet data format  

**정답: D) Apache Parquet data format**

💡 추가 설명:
- **A (JSON)**: 반정형 데이터에는 유용하지만 컬럼 최적화 미지원, 데이터 크기 증가.  
- **B (gzip CSV)**: 저장 비용 절감은 가능하나 row-based 포맷이라 쿼리 성능 최적화 불가.  
- **C (XML)**: Athena 직접 지원 불가 → 비효율적.  
- **D (Parquet)**: 컬럼 기반, 압축 효율적, Athena/Redshift Spectrum 최적화 → 가장 적합.  
