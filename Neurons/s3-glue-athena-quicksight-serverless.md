---

title: s3-glue-athena-quicksight-serverless
created: 2025-08-19
modified: 2025-08-19
tags:
- service/s3
- service/glue
- service/athena
- service/quicksight
- constraint/serverless
aliases: ["s3-analytics", "serverless-bi", "clickstream-analysis"]

---

# S3 데이터레이크 기반 서버리스 분석 파이프라인

## 🎯 핵심 포인트

서버 프로비저닝 없이 S3 데이터레이크의 클릭스트림 데이터를 분석하고 시각화하는 경우, S3 + Glue + Athena + QuickSight 조합을 사용할 수 있다.

## 📝 설명

### 서버리스 분석 아키텍처가 대용량 데이터 분석에 적합한 이유

이 조합은 서버 관리 부담 없이 확장 가능한 데이터 분석 환경을 제공합니다. 각 서비스가 완전 관리형으로 운영되어 인프라 운영비용을 최소화하면서도 필요에 따라 자동으로 스케일링됩니다. 특히 클릭스트림과 같은 대용량 비정형 데이터를 효율적으로 처리할 수 있습니다.

### 아키텍처 플로우

```
원시 클릭스트림 데이터 
    ↓ (저장)
S3 데이터 레이크
    ↓ (ETL 및 카탈로그)
AWS Glue (데이터 정리 + 스키마 정의)
    ↓ (SQL 쿼리 실행)
Amazon Athena (대화형 분석)
    ↓ (데이터 시각화)
Amazon QuickSight (대시보드 및 리포트)
```

### 각 서비스의 역할과 상호작용

**AWS Glue**
- 크롤러를 통한 자동 스키마 감지 및 데이터 카탈로그 생성
- ETL 작업으로 데이터 정리 및 피처 엔지니어링
- 파케이 포맷 변환으로 쿼리 성능 최적화

**Amazon Athena**
- 사용자가 작성한 SQL 쿼리를 S3 데이터에 대해 직접 실행
- 쿼리 결과를 QuickSight가 활용할 수 있는 형태로 반환
- 파티셔닝과 압축을 통한 비용 최적화

**QuickSight 시각화 프로세스**
1. SQL 쿼리 실행이 반드시 선행 (Athena에서)
2. 쿼리 결과 데이터를 QuickSight 데이터셋으로 가져오기
3. 시각적 차트 및 대시보드 생성

### Trade-offs 고려사항

**서버리스 조합 장점**:
- 서버 프로비저닝 및 관리 불필요
- 사용한 만큼만 비용 지불 (pay-per-query)
- 자동 스케일링으로 트래픽 변동 대응
- 높은 가용성 및 내구성

**서버리스 조합 단점**:
- 쿼리 시작 시 콜드 스타트 지연
- 복잡한 실시간 분석에는 제약
- 쿼리당 비용이 높은 경우 전체 비용 증가

**EMR 클러스터 장점**:
- 지속적인 분석 워크로드에 비용 효율적
- Spark/Hadoop 기반 복잡한 분석 가능

**EMR 클러스터 단점**:
- 클러스터 관리 및 모니터링 필요
- 고정 비용 발생 (사용하지 않을 때도)

## 🔍 주요개념

### 서버리스 vs 서버 기반 서비스

- **서버리스 서비스**: S3, Glue, Athena, QuickSight - 인프라 관리 불필요
- **서버 기반 서비스**: EMR, OpenSearch, RDS - 클러스터/인스턴스 프로비저닝 필요

### 실전 적용

- **전자상거래 클릭스트림 분석**: 고객 여정 추적 및 전환율 분석
- **웹사이트 성능 모니터링**: 페이지 로딩 시간 및 사용자 행동 패턴
- **마케팅 캠페인 효과 측정**: UTM 파라미터 기반 트래픽 분석

## 📝 관련 문제

**Question:** You are managing a large S3 data lake filled with clickstream data and aim to conduct analysis and visualization to comprehend the necessary cleaning and feature engineering steps. Without provisioning individual servers, which AWS services would enable you to analyze and visualize this data?

**Options:**

- A) S3, EMR, QuickSight
- B) S3, Kinesis, Amazon OpenSearch  
- C) S3, Glue, Athena, and QuickSight
- D) S3, DMS, RDS

**정답: C) S3, Glue, Athena, and QuickSight**

💡 추가 설명:

- **EMR (옵션 A)** - Hadoop/Spark 클러스터 프로비저닝이 필요한 서버 기반 서비스
- **Amazon OpenSearch (옵션 B)** - 검색 클러스터 관리가 필요한 서버 기반 서비스  
- **RDS (옵션 D)** - 데이터베이스 인스턴스 프로비저닝이 필요하며 분석 용도에 부적합