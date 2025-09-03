---

title: streaming-kinesis-firehose-serverless
created: 2025-08-18
modified: 2025-08-18
tags:
- service/kinesis-firehose
- constraint/serverless
- storage/parquet
- analytics/real-time
- usecase/demographic-analysis
aliases: ["kinesis-firehose", "streaming-analytics", "serverless-etl"]

---

# 스트리밍 데이터 분석을 위한 Kinesis Data Firehose 기반 서버리스 아키텍처

## 🎯 핵심 포인트

연속적인 JSON 데이터를 실시간으로 처리하고 분석해야 하는 경우 서버리스 환경에서, Kinesis Data Firehose를 통해 Parquet 형태로 변환하여 S3 데이터 레이크에 저장하고 Athena와 QuickSight로 분석할 수 있다.

## 📝 설명

### Kinesis Data Firehose가 스트리밍 데이터 분석에 적합한 이유

Amazon Kinesis Data Firehose는 실시간 스트리밍 데이터를 자동으로 수집하고 변환하여 목적지로 전송하는 완전관리형 서비스입니다. 특히 JSON 형태의 원시 데이터를 Apache Parquet 또는 ORC와 같은 컬럼형 데이터 형식으로 변환하여 저장할 수 있어, 분석 성능을 크게 향상시키면서 스토리지 비용을 절감할 수 있습니다. 서버 관리가 불필요한 완전 서버리스 서비스로, 인프라 운영 오버헤드 없이 확장성과 비용 효율성을 동시에 달성할 수 있습니다.

### 아키텍처 플로우

```
JSON 스트리밍 데이터
        ↓
Kinesis Data Firehose (데이터 수집 및 변환)
        ↓
JSON → Parquet 형식 변환
        ↓
Amazon S3 Data Lake (저장)
        ↓
AWS Glue Data Catalog (메타데이터 관리)
        ↓
Amazon Athena (쿼리 엔진)
        ↓
Amazon QuickSight (시각화 및 분석)
```

### Trade-offs 고려사항

**Kinesis Data Firehose 장점**:
- 완전 서버리스로 인프라 관리 불필요
- 실시간 데이터 형식 변환 (JSON → Parquet)
- 자동 스케일링 및 내결함성 제공
- Parquet 변환으로 스토리지 비용 75% 절감
- 분석 쿼리 성능 대폭 향상

**Kinesis Data Firehose 단점**:
- 최소 60초 버퍼링 지연 존재
- 복잡한 데이터 변환 로직 제한
- 실시간 스트리밍 분석에는 부적합 (준실시간)

**Aurora Database 장점**:
- 즉시 쿼리 가능한 관계형 구조
- ACID 트랜잭션 보장
- 복잡한 조인 쿼리 최적화

**Aurora Database 단점**:
- 서버 관리 및 스케일링 필요
- 높은 운영 비용
- 분석 워크로드 최적화 부족
- 대용량 스트리밍 데이터 처리 한계

## 🔍 주요개념

### 데이터 형식 비교

- **JSON 형식**: 사람이 읽기 쉬운 텍스트 형식이지만 스토리지 효율성과 쿼리 성능이 낮음
- **Parquet 형식**: 컬럼형 저장 구조로 압축률이 높고 분석 쿼리에 최적화된 형식

### 실전 적용

- **디지털 마케팅 회사**: 실시간 고객 행동 데이터 수집 및 세그멘테이션 분석
- **전자상거래 플랫폼**: 사용자 클릭스트림 데이터 분석을 통한 개인화 추천
- **IoT 데이터 처리**: 센서 데이터 수집 및 실시간 모니터링 대시보드 구성

## 📝 관련 문제

**Question:** A digital marketing firm needs to analyze and visualize consumer demographic information, which arrives continuously in JSON format. The data includes various attributes like age, income, and geographical location among others. The firm aims to achieve this with high efficiency and minimal costs while avoiding the overhead associated with managing servers. What is the most appropriate solution to accomplish these objectives?

**Options:**

- A) Stream the data into an Amazon Aurora database for immediate querying capabilities. Leverage Aurora's JDBC integration for data visualization through Amazon QuickSight.
- B) Leverage Amazon Kinesis Firehose for data transformation into Parquet format and subsequent storage in an Amazon S3 data lake. Utilize AWS Glue to catalog the data, Amazon Athena for querying, and Amazon QuickSight for visualization and analysis.
- C) Implement Amazon Kinesis Data Streams for direct storage of the data in Amazon S3. Employ an Amazon EMR cluster for the conversion of data into Parquet format. Further, use AWS Glue for data cataloging, Amazon Athena for querying, and Amazon QuickSight for data visualization and analysis.
- D) Utilize Amazon Kinesis Firehose for direct storage of data in Amazon S3 in its native JSON format. Employ Amazon QuickSight for the visualization and analysis of the data.

**정답: B) Amazon Kinesis Data Firehose + Parquet + S3 Data Lake + Glue + Athena + QuickSight**

💡 추가 설명:

- **옵션 A (Aurora Database)** - 서버리스 요구사항을 충족하지 못하며, 분석 워크로드보다 트랜잭션 처리에 최적화되어 비용 효율성이 떨어짐
- **옵션 C (EMR Cluster)** - EMR 클러스터 관리가 필요하여 서버리스 요구사항에 부합하지 않으며, 운영 복잡성과 비용이 증가함
- **옵션 D (JSON 형식 저장)** - Parquet 변환 없이 JSON으로 저장하면 스토리지 비용과 쿼리 성능 면에서 비효율적이며, 대용량 분석에 부적합함