---

title: dms-mysql-s3-cdc
created: 2025-08-17
modified: 2025-08-17
tags:
- service/dms
- source/mysql
- target/s3
- technique/cdc
- usecase/ml-pipeline
aliases: ["DMS", "Database Migration Service", "CDC", "Change Data Capture", "mysql-s3"]

---

# AWS DMS를 활용한 MySQL 데이터베이스 S3 마이그레이션 및 실시간 동기화

## 🎯 핵심 포인트

온프레미스 MySQL 데이터베이스를 S3로 복사하고 지속적인 변경사항을 실시간으로 스트리밍해야 하는 경우 AWS DMS에서, 전체 데이터 로드와 CDC(Change Data Capture)를 통해 무중단 데이터 파이프라인을 구축할 수 있다.

## 📝 설명

### AWS DMS가 MySQL-S3 마이그레이션에 적합한 이유

AWS Database Migration Service(DMS)는 데이터베이스 마이그레이션과 지속적인 데이터 복제를 위해 설계된 완전 관리형 서비스입니다. MySQL과 같은 관계형 데이터베이스에서 S3로의 마이그레이션에 특히 적합한 이유는 다음과 같습니다:

**전체 로드(Full Load) 지원**: 기존 MySQL 데이터 전체를 일괄적으로 S3로 복사하며, 기본적으로 CSV 형식으로 저장됩니다. 더 효율적인 저장과 빠른 쿼리를 위해 Apache Parquet 형식도 지원합니다.

**변경 데이터 캡처(CDC)**: 초기 마이그레이션 완료 후, MySQL의 바이너리 로그를 분석하여 실시간으로 변경사항(INSERT, UPDATE, DELETE)을 감지하고 S3에 스트리밍합니다.

**SageMaker 연동 최적화**: S3에 저장된 데이터는 SageMaker의 Pipe 모드와 완벽하게 호환되어, EBS 다운로드 없이 직접 스트리밍하여 훈련 시작 시간을 단축시킵니다.

### 아키텍처 플로우

```
[On-premises MySQL] 
        ↓ (Full Load)
[AWS DMS Replication Instance]
        ↓ (CSV/Parquet)
[Amazon S3 Bucket]
        ↑ (Ongoing CDC)
[MySQL Binary Logs] → [DMS CDC] → [S3 Delta Files]
        ↓
[SageMaker Pipe Mode] → [ML Model Training]
```

### Trade-offs 고려사항

**AWS DMS 장점**:
- 무중단 마이그레이션 지원 (프로덕션 영향 최소화)
- 자동 스키마 변환 및 데이터 타입 매핑
- 내장된 CDC 기능으로 실시간 동기화
- S3 타겟에서 자동 CSV/Parquet 변환
- 데이터 검증 및 오류 처리 기능

**AWS DMS 단점**:
- 초기 설정 및 구성의 복잡성
- 대용량 데이터 마이그레이션 시 시간 소요
- 네트워크 대역폭에 따른 성능 제약
- CDC 지연 시간 (수초~수분)

**AWS Glue 장점**:
- 강력한 ETL 변환 기능
- 서버리스 아키텍처
- 다양한 데이터 소스 지원

**AWS Glue 단점**:
- 실시간 CDC 기능 부재
- 지속적인 데이터베이스 동기화에 부적합
- 배치 처리 중심 설계

**Amazon Data Firehose 장점**:
- 실시간 스트리밍 데이터 수집
- 자동 압축 및 형식 변환
- 서버리스 운영

**Amazon Data Firehose 단점**:
- 데이터베이스 직접 연결 불가
- 초기 전체 데이터 로드 기능 없음
- 스키마 변환 기능 부족

**AWS Snowball 장점**:
- 대용량 데이터 안전한 물리적 전송
- 네트워크 제약 없는 데이터 이동

**AWS Snowball 단점**:
- 일회성 전송만 가능
- 지속적인 동기화 불가능
- 물리적 배송으로 인한 시간 지연

## 🔍 주요개념

### 핵심 개념 비교

- **Full Load**: 소스 데이터베이스의 모든 기존 데이터를 타겟으로 일괄 복사하는 초기 마이그레이션 단계
- **Change Data Capture (CDC)**: 데이터베이스의 변경사항을 실시간으로 감지하고 복제하는 지속적 동기화 메커니즘
- **Pipe Mode**: SageMaker의 고성능 데이터 입력 방식으로, S3에서 직접 스트리밍하여 훈련 시작 시간과 스토리지 비용을 절약
- **Replication Instance**: DMS에서 마이그레이션 작업을 실행하는 EC2 기반 컴퓨팅 리소스

### 실전 적용

- **전자상거래 추천 시스템**: 고객 행동 데이터를 실시간으로 S3에 복제하여 SageMaker에서 개인화 모델 훈련
- **금융 사기 탐지**: 거래 데이터의 실시간 변경사항을 S3로 스트리밍하여 이상 탐지 모델에 즉시 반영
- **IoT 센서 데이터 분석**: 제조업체의 센서 데이터베이스를 S3로 지속 복제하여 예측 유지보수 모델 개발

## 📝 관련 문제

**Question:** A Machine Learning Specialist has an on-premises MySQL database that needs to be replicated in Amazon S3 as CSV files. Once the data has been fully copied, on-going changes to the database should be continually streamed into the S3 bucket. The Amazon SageMaker's Pipe input mode will be used to fetch datasets from Amazon S3 for training the ML models. Which service will help the ML Specialist automate these tasks efficiently?

**Options:**

- A) AWS Glue
- B) Amazon Data Firehose  
- C) AWS DMS
- D) AWS Snowball

**정답: C) AWS DMS**

💡 추가 설명:

- **AWS Glue** - ETL 작업에는 적합하지만 실시간 CDC 기능이 없어 지속적인 데이터베이스 동기화에 부적합
- **Amazon Data Firehose** - 스트리밍 데이터 수집에 특화되어 있지만 데이터베이스 직접 연결 및 초기 전체 로드 기능 부재
- **AWS Snowball** - 일회성 대용량 데이터 전송용 물리적 디바이스로 지속적인 변경사항 복제 불가능
- **AWS DMS** - 전체 로드와 CDC를 모두 지원하며, S3 타겟에서 자동으로 CSV 형식 변환, SageMaker Pipe 모드와 완벽 호환