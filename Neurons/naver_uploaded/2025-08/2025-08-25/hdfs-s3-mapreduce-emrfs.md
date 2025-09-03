---

title: hdfs-s3-mapreduce-emrfs
created: 2025-08-18
modified: 2025-08-18
tags:
- service/emrfs
- migration/hdfs-to-s3
- platform/amazon-emr
- storage/s3-data-lake
- processing/mapreduce
aliases: ["hdfs-s3-migration", "emrfs-integration", "mapreduce-s3"]

---

# HDFS에서 S3로 마이그레이션한 MapReduce 작업 최적화

## 🎯 핵심 포인트

레거시 MapReduce 작업이 HDFS에서 S3 데이터 레이크로 마이그레이션된 경우 Amazon EMR에서, EMRFS를 활용하여 s3:// 프리픽스로 투명한 데이터 접근이 가능하다.

## 📝 설명

### EMRFS가 MapReduce-S3 통합에 적합한 이유

EMRFS(Amazon EMR File System)는 Hadoop File System(HDFS) API의 구현체로, EMR 클러스터의 애플리케이션이 Amazon S3의 데이터를 HDFS처럼 직접 접근할 수 있게 해줍니다. 기존 MapReduce 코드의 파일 경로만 `hdfs://`에서 `s3://`로 변경하면 되므로 코드 수정을 최소화할 수 있습니다.

EMRFS는 EMR에 내장되어 있어 별도 설정 없이 바로 사용 가능하며, S3의 eventual consistency 문제를 해결하는 Consistent View 기능, IAM 역할 기반 보안, 메타데이터 캐싱을 통한 성능 최적화 등 추가 기능을 제공합니다.

### 아키텍처 플로우

```
Legacy HDFS Environment:
MapReduce Job → HDFS API → Local HDFS Storage

Migrated S3 Environment:
MapReduce Job → EMRFS (HDFS API) → s3:// prefix → Amazon S3 Data Lake
                     ↓
            [Consistent View + Metadata Cache + Security]
```

### Trade-offs 고려사항

**EMRFS 장점**:
- HDFS API 완전 호환으로 코드 변경 최소화
- EMR 내장 기능으로 별도 설정 불필요
- Consistent View로 S3 일관성 문제 해결
- IAM 역할 및 암호화 지원으로 보안 강화
- 메타데이터 캐싱으로 성능 최적화

**EMRFS 단점**:
- EMR 환경에서만 사용 가능
- S3 스토리지 비용 발생
- 네트워크 레이턴시로 인한 성능 차이 가능

**s3a:// 직접 접근 장점**:
- 표준 Hadoop 클라이언트에서 사용 가능
- 오픈소스 기반으로 벤더 락인 없음

**s3a:// 직접 접근 단점**:
- EMR 최적화 기능 부족
- Consistent View 기능 없음
- 추가 보안 설정 필요

## 🔍 주요개념

### 핵심 개념 비교

- **EMRFS**: Amazon EMR에 특화된 S3 접근 레이어로 HDFS API 완전 호환 및 추가 최적화 기능 제공
- **s3a://**: 표준 Hadoop S3 클라이언트로 범용적 사용 가능하나 EMR 특화 기능 부족
- **Consistent View**: EMRFS의 핵심 기능으로 S3의 eventual consistency 문제를 해결하여 데이터 일관성 보장

### 실전 적용

- **ETL 파이프라인 마이그레이션**: 기존 Hadoop ETL 작업을 S3 기반으로 전환할 때 코드 변경 최소화
- **데이터 레이크 구축**: 온프레미스 HDFS에서 AWS S3 데이터 레이크로 전환하는 클라우드 마이그레이션
- **하이브리드 환경**: 일부 데이터는 HDFS에, 일부는 S3에 저장하는 혼합 환경에서 통일된 접근 방식 제공

## 📝 관련 문제

**Question:** A legacy MapReduce job that previously operated on data stored in HDFS is now required to process data that has been migrated to an Amazon S3 data lake. Given this change, what is a feasible approach for integrating the S3-stored data with MapReduce jobs running on Amazon EMR?

**Options:**

- A) Utilize Amazon EMR File System (EMRFS) to establish a connection between MapReduce and S3, employing the s3:// file prefix for data access
- B) Enable MapReduce to communicate directly with S3 using the s3a:// prefix without additional configurations
- C) Implement Amazon Elastic File System (EFS) to bridge the connection between MapReduce and the S3 bucket
- D) Deploy Apache Hive as an intermediary layer to facilitate data access between MapReduce and S3

**정답: A) Amazon EMR File System (EMRFS)**

💡 추가 설명:

- **Option B (s3a://)** - 가능하지만 EMRFS만큼 EMR에 최적화되지 않으며 일관성 보장 및 보안 기능 부족
- **Option C (EFS)** - 파일 시스템과 객체 스토리지 간 불필요한 중간 단계로 성능 오버헤드 및 비용 증가
- **Option D (Apache Hive)** - SQL 접근이 필요한 경우가 아니면 과도한 복잡성 추가 및 불필요한 설정