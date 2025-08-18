---

title: bigdata-emr-quicksight-visualization
created: 2025-08-18
modified: 2025-08-18
tags:
- service/emr
- service/quicksight
- constraint/high-volume
- usecase/ml-insights
- deployment/batch-processing
aliases: ["EMR-QuickSight", "big-data-viz", "ml-dashboard"]

---

# Amazon EMR과 QuickSight를 활용한 대용량 ML 데이터 시각화

## 🎯 핵심 포인트

매일 150TB의 대용량 예측 데이터를 처리하고 비즈니스팀과 공유해야 하는 경우, Amazon EMR로 데이터를 처리하고 S3에 저장한 후 QuickSight로 대시보드를 생성하여 최소한의 개발 노력으로 확장 가능한 ML 인사이트 시각화를 구현할 수 있다.

## 📝 설명

### Amazon EMR이 대용량 ML 데이터 처리에 적합한 이유

Amazon EMR(Elastic MapReduce)은 빅데이터 처리를 위한 관리형 클러스터 플랫폼으로, 150TB와 같은 대용량 데이터를 효율적으로 처리할 수 있습니다. 자동 스케일링 기능을 통해 데이터 양에 따라 동적으로 클러스터 크기를 조정하며, Apache Spark, Hadoop, Hive 등의 오픈소스 빅데이터 프레임워크를 완전 관리형으로 제공합니다.

precision-recall 커브와 같은 ML 메트릭 계산은 대량의 예측 결과를 집계하고 분석해야 하므로, EMR의 분산 처리 능력이 필수적입니다. 또한 일일 배치 작업으로 스케줄링하여 자동화된 데이터 파이프라인을 구축할 수 있습니다.

### 아키텍처 플로우

```
SageMaker Endpoint (150TB/day)
    ↓
EMR Cluster (Daily Workflow)
    ↓ precision-recall 계산
S3 Bucket (처리된 결과 저장)
    ↓
QuickSight Dashboard
    ↓
Business Team (Read-only Access)
```

### Trade-offs 고려사항

**Amazon EMR 장점**:
- 대용량 데이터 처리에 최적화된 분산 컴퓨팅
- 자동 스케일링으로 비용 효율성
- 다양한 빅데이터 도구 지원 (Spark, Hive, Presto)
- 배치 처리 워크플로우에 적합

**Amazon EMR 단점**:
- 실시간 처리에는 부적합
- 클러스터 시작 시간 오버헤드
- 소규모 데이터에는 오버스펙

**Amazon QuickSight 장점**:
- 서버리스 BI 서비스로 관리 오버헤드 없음
- ML 기반 인사이트 자동 생성
- 다양한 데이터 소스 연결 지원
- 쉬운 대시보드 공유 기능

**CloudWatch 단점**:
- S3 데이터로 직접 커스텀 대시보드 생성 불가
- 복잡한 워크어라운드 필요
- ML 메트릭 시각화에 부적합

**Redshift 단점**:
- 이 시나리오에서 불필요한 추가 복잡성
- EMR 결과 저장만을 위한 오버헤드
- S3 직접 연결로도 충분한 상황

## 🔍 주요개념

### 데이터 처리 방식 비교

- **배치 처리 (EMR)**: 대량 데이터를 정기적으로 일괄 처리하는 방식으로, 높은 처리량과 비용 효율성 제공
- **실시간 처리 (Kinesis)**: 스트리밍 데이터를 즉시 처리하지만 대용량 배치 작업에는 부적합

### 실전 적용

- **일일 ML 모델 성능 모니터링**: 프로덕션 모델의 precision, recall, F1-score 추적
- **A/B 테스트 결과 분석**: 여러 모델 버전의 성능 비교 대시보드
- **비즈니스 KPI 연결**: ML 메트릭을 비즈니스 성과와 연결한 통합 대시보드

## 📝 관련 문제

**Question:** A company has deployed an Amazon SageMaker endpoint in production to generate predictions. The company is expecting 150TB of predictions every day. Its Machine Learning Specialist needs daily visual insights to the prediction's precision-recall curve. Also, the Business Team requires a read-only visual representation of this data. Which approach should the Specialist do that requires the LEAST amount of development effort?

**Options:**

- A) Generate the precision-recall data by executing a daily Amazon EMR workflow. Store the results in an S3 bucket. Produce a dashboard of the results in Amazon CloudWatch and share it with the business team.
- B) Generate the precision-recall data by executing a daily Amazon EMR workflow. Store the results to an S3 bucket and configure its bucket policy to give read-only access to its business team.
- C) Generate the precision-recall data by executing a daily Amazon EMR workflow. Store the results in a Redshift table. Then, produce a dashboard of the results in Amazon QuickSight and share it with the business team.
- D) Generate the precision-recall data by executing a daily Amazon EMR workflow. Store the results in an S3 bucket. Produce a dashboard of the results in Amazon QuickSight and share it with the business team.

**정답: D) Amazon EMR → S3 → QuickSight 파이프라인**

💡 추가 설명:

- **옵션 A (CloudWatch)** - S3 데이터로 직접 커스텀 CloudWatch 대시보드 생성이 복잡하고 비실용적
- **옵션 B (S3 직접 접근)** - S3는 객체 스토리지로 데이터 시각화 기능이 없어 비즈니스팀이 활용 불가
- **옵션 C (Redshift 경유)** - EMR 결과 저장을 위한 Redshift는 불필요한 복잡성과 비용 추가