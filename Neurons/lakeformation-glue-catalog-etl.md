---

title: lakeformation-glue-catalog-etl
created: 2025-08-20
modified: 2025-08-20
tags:
- service/lakeformation
- service/glue
- usecase/data-catalog
- technique/etl
- constraint/multi-source
aliases: ["lake-formation", "glue-integration", "data-catalog"]

---

# AWS Lake Formation과 Glue를 이용한 통합 데이터 카탈로그 및 ETL

## 🎯 핵심 포인트

여러 데이터 소스(S3, RDS, DynamoDB)를 머신러닝 목적으로 수집하고 자동 스키마 추론과 데이터 카탈로그가 필요한 경우, Lake Formation에서 중앙 관리하고 Glue에서 실제 카탈로깅 및 ETL 작업을 수행할 수 있다.

## 📝 설명

### Lake Formation과 Glue가 데이터 카탈로깅에 적합한 이유

Lake Formation은 데이터 레이크의 **중앙 관리 계층**으로 작동하며, 실제 카탈로그 생성과 ETL 작업은 **AWS Glue**가 담당합니다. Lake Formation은 "오케스트레이터" 역할을 하고, Glue가 "실행자" 역할을 수행하는 구조입니다.

**Lake Formation 역할**:
- 데이터 소스 등록 및 중앙 관리
- 세분화된 권한 제어 (테이블/컬럼/행 레벨)
- 보안 정책 및 거버넌스 관리
- 워크플로 관리 및 Blueprint 제공

**AWS Glue 역할**:
- 실제 데이터 크롤링 및 스키마 발견
- 자동 카탈로그 생성 (Glue Data Catalog)
- ETL 작업 실행 및 데이터 변환
- 메타데이터 관리

### 아키텍처 플로우

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Data Sources  │    │  Lake Formation  │    │  AWS Services   │
├─────────────────┤    ├──────────────────┤    ├─────────────────┤
│ • S3            │────▶ • 소스 등록      │────▶ • Glue Crawler  │
│ • RDS           │    │ • 권한 관리      │    │ • Glue Jobs     │
│ • DynamoDB      │    │ • 정책 설정      │    │ • Glue Catalog  │
│ • Redshift      │    │ • 워크플로 관리   │    │ • Athena Query  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                              │
                              ▼
                    ┌──────────────────┐
                    │  Unified Catalog │
                    │ • 통합 스키마     │
                    │ • 권한 적용      │
                    │ • Cross-source   │
                    │   Query 지원     │
                    └──────────────────┘
```

### Trade-offs 고려사항

**Lake Formation + Glue 장점**:
- 다양한 데이터 소스의 통합 관리
- 자동 스키마 추론 및 카탈로그 생성
- 세분화된 권한 제어 (컬럼/행 레벨)
- Cross-source 쿼리 지원
- 중앙집중식 거버넌스

**Lake Formation + Glue 단점**:
- 초기 설정 복잡성
- 추가 서비스 비용 발생
- Glue Crawler 실행 시간 소요
- 복잡한 권한 모델 학습 필요

**Glue 단독 사용 장점**:
- 단순한 ETL 워크플로
- 빠른 구현 가능
- 직관적인 설정

**Glue 단독 사용 단점**:
- 통합 권한 관리 부족
- 다중 소스 관리 복잡성
- 거버넌스 기능 제한

## 🔍 주요개념

### 서비스 역할 비교

- **Lake Formation**: 데이터 레이크 중앙 관리자, 권한 제어, 정책 관리
- **AWS Glue**: 실제 카탈로그 생성, ETL 작업 수행, 스키마 추론
- **Glue Data Catalog**: 모든 메타데이터가 저장되는 중앙 저장소

### 실전 적용

- **다중 소스 통합**: S3, RDS, DynamoDB를 하나의 카탈로그로 통합 관리
- **권한 세분화**: 부서별/역할별로 테이블, 컬럼, 행 레벨 접근 제어
- **자동화 파이프라인**: 크롤러 스케줄링으로 스키마 변경사항 자동 반영

## 📝 관련 문제

**Question:** A machine learning team needs to collect data from multiple sources including S3 buckets, RDS databases, and DynamoDB tables for training models. They require automatic schema inference, centralized data cataloging, and fine-grained access control. Which combination of AWS services would be most appropriate?

**Options:**

- A) Amazon Kinesis Data Analytics + Amazon QuickSight
- B) AWS Step Functions + Amazon Athena
- C) AWS Lake Formation + AWS Glue
- D) Amazon EMR + AWS Lambda
- E) AWS DataSync + Amazon Redshift

**정답: C) AWS Lake Formation + AWS Glue**

💡 추가 설명:

- **A) Kinesis Data Analytics + QuickSight** - 실시간 스트리밍 분석과 시각화에 특화, 데이터 카탈로깅 기능 없음
- **B) Step Functions + Athena** - 워크플로 관리와 쿼리 서비스, 자동 스키마 추론 기능 부족
- **D) EMR + Lambda** - 빅데이터 처리와 서버리스 컴퓨팅, 통합 카탈로그 관리 기능 없음
- **E) DataSync + Redshift** - 데이터 전송과 웨어하우스, 다중 소스 카탈로깅에 부적합