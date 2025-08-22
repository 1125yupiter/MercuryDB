---

title: datasync-migration-objectstorage-s3
created: 2025-01-21
modified: 2025-01-21
tags:
  - service/datasync
  - migration/file-object
  - storage/s3
  - sync/scheduled
  - onpremise/hybrid
aliases: ["datasync", "file-migration", "object-sync"]

---

# AWS DataSync for On-Premises Object Storage to S3 Migration

## 🎯 핵심 포인트

온프레미스 객체 스토리지에서 Amazon S3로 파일 데이터를 지속적으로 동기화해야 하는 경우, AWS DataSync를 사용하여 암호화, 스케줄링, 모니터링, 데이터 무결성 검사를 모두 지원하는 자동화된 마이그레이션을 구현할 수 있다.

## 📝 설명

### AWS DataSync가 파일/객체 마이그레이션에 적합한 이유

AWS DataSync는 온프레미스 스토리지 시스템과 AWS 스토리지 서비스 간의 데이터 전송을 위해 특별히 설계된 서비스입니다. NFS, SMB, HDFS와 같은 파일 시스템과 S3, EFS, FSx 등 AWS 스토리지 서비스 간의 데이터 이동에 최적화되어 있으며, 파일 메타데이터와 권한 정보를 보존하면서 대용량 파일 전송을 효율적으로 처리합니다.

**핵심 기능:**
- **자동 증분 동기화**: 변경된 파일만 감지하여 전송
- **스케줄 기반 실행**: 정기적인 동기화 작업 자동화
- **데이터 무결성 검증**: 전송 중 및 전송 후 데이터 검증
- **암호화 지원**: 전송 중(TLS) 및 저장 시(KMS) 암호화
- **대역폭 제어**: 네트워크 영향 최소화를 위한 스로틀링

### 아키텍처 플로우

```
온프레미스 객체 스토리지 (40TB)
          ↓
    DataSync Agent (EC2/VMware/Hyper-V)
          ↓
      AWS DataSync Service
          ↓
        Amazon S3
          ↓
    CloudWatch Monitoring
```

**프로세스:**
1. 초기 전체 데이터 복사 (Full Load)
2. 스케줄된 증분 동기화 (변경사항만)
3. 데이터 무결성 검증 및 모니터링
4. 최종 컷오버까지 지속적 동기화

### Trade-offs 고려사항

**AWS DataSync 장점**:
- 파일 메타데이터 및 권한 보존
- 스케줄 기반 자동 동기화
- 네트워크 최적화된 전송 프로토콜
- VPC 엔드포인트 지원으로 보안 강화
- 40TB 규모의 대용량 데이터 처리 최적화

**AWS DataSync 단점**:
- 실시간 동기화 아닌 스케줄 기반
- 네트워크 대역폭에 의존적
- 에이전트 설치 및 관리 필요

**AWS DMS 장점**:
- 실시간 CDC(Change Data Capture) 지원
- 데이터베이스 간 스키마 변환 가능
- 지속적인 복제로 거의 제로 다운타임

**AWS DMS 단점**:
- 데이터베이스 데이터만 처리 가능
- 파일/객체 스토리지 직접 지원 안함
- 객체 스토리지 → S3 시나리오에 부적합

## 🔍 주요개념

### 서비스별 데이터 타입 구분

- **AWS DataSync**: 파일, 폴더, 객체 스토리지 (NFS, SMB, HDFS → S3, EFS, FSx)
- **AWS DMS**: 데이터베이스 테이블, 스키마 (Oracle, MySQL, PostgreSQL → RDS, S3, DynamoDB)

### 실전 적용

- **머신러닝 프로젝트**: 온프레미스 데이터 레이크를 S3로 마이그레이션하여 SageMaker 활용
- **백업 아카이브**: 온프레미스 파일 서버 데이터를 S3 Glacier로 비용 효율적 보관
- **하이브리드 클라우드**: 온프레미스와 클라우드 간 데이터 동기화로 재해 복구 구현

## 📝 관련 문제

**Question:** A business wishes to establish a data repository on AWS for machine learning projects. The company intends to store data using Amazon S3 and manage the entire ML lifecycle on AWS. All company data is currently stored on-premises in object storage and measures 40TB. The organization needs a solution for automated data transfer and updates between on-premises object storage and Amazon S3. The system must support encryption, scheduling, monitoring, and data integrity checking. Which solution satisfies these criteria?

**Options:**

- A) Use the S3 sync command to compare source and destination S3 buckets and transfer modified files
- B) Use AWS Transfer for FTPS to transfer files from on-premises storage to Amazon S3
- C) Use AWS DataSync to make an initial copy of the entire dataset and schedule subsequent incremental transfers until final cutover
- D) Use S3 Batch Operations to pull data periodically from on-premises storage with S3 Versioning enabled
- E) Use AWS DMS with CDC to replicate changes from on-premises object storage to S3

**정답: C) AWS DataSync**

💡 추가 설명:

- **선택지 A** - S3 sync 명령어는 수동 실행이며 스케줄링, 모니터링 기능이 제한적
- **선택지 B** - AWS Transfer for FTPS는 일회성 전송에 적합하며 지속적 동기화 지원 안함
- **선택지 D** - S3 Batch Operations는 S3 내 데이터 처리용이며 온프레미스 연결 불가
- **선택지 E** - AWS DMS는 데이터베이스 전용이며 객체 스토리지 직접 마이그레이션 지원 안함