---

title: etl-glue-serverless-pipeline
created: 2025-08-27
modified: 2025-08-27
tags:
- service/glue
- constraint/large-scale
- problem/etl-pipeline
- deployment/serverless
- usecase/data-processing
aliases: ["glue-etl", "serverless-etl", "glue-pipeline"]

---

# AWS Glue를 활용한 대규모 ETL 파이프라인 구축

## 🎯 핵심 포인트

테라바이트급 데이터를 20시간 이상 처리하는 복잡한 ETL 파이프라인의 경우 서버리스 환경에서, AWS Glue를 통해 서버 관리 부담 없이 효율적으로 처리할 수 있다.

## 📝 설명

### AWS Glue가 대규모 ETL 파이프라인에 적합한 이유

AWS Glue는 Apache Spark 기반의 완전 관리형 ETL 서비스로, 테라바이트급 데이터 처리와 장시간 실행 작업에 최적화되어 있습니다. 기존 Python 스크립트를 PySpark로 변환하여 분산 처리 환경에서 실행할 수 있으며, 자동 스케일링을 통해 처리 시간을 대폭 단축시킵니다.

핵심 특징:
- **시간 제한 없음**: Lambda의 15분 제한과 달리 몇 시간에서 하루 종일도 실행 가능
- **자동 분산 처리**: 단일 머신에서 20시간 → 분산 환경에서 2-6시간으로 단축
- **완전 관리형**: EC2 인스턴스 관리, 패치, 업데이트 불필요
- **비용 효율성**: 작업 시에만 리소스 사용, 자동 스케일링으로 필요한 만큼만 과금

### 아키텍처 플로우

```
S3 Raw Data (테라바이트급)
    ↓
AWS Glue Job (PySpark)
    ↓
[분산 처리 클러스터]
- 데이터 정리 (Clean)
- 데이터 변환 (Transform)  
- 데이터 강화 (Enrich)
- 데이터 압축 (Compress)
    ↓
S3 Processed Data
    ↓
CloudWatch 모니터링 & SNS 알림
```

### Trade-offs 고려사항

**AWS Glue 장점**:
- 완전 서버리스, 인프라 관리 불필요
- 테라바이트급 데이터 처리에 최적화
- 자동 스케일링과 분산 처리로 시간 단축
- Python 로직을 PySpark로 변환하여 기존 비즈니스 로직 재활용 가능
- 체크포인팅으로 실패 시 중간 지점부터 재시작

**AWS Glue 단점**:
- PySpark 학습 곡선 필요
- 순수 Python 라이브러리 사용 제한
- 디버깅 복잡성 (지연 평가로 인한)
- 초기 설정 및 튜닝 필요

**AWS Lambda 장점**:
- 완전 서버리스, 이벤트 기반 처리
- 빠른 시작 시간, 간단한 배포

**AWS Lambda 단점**:
- **15분 실행 시간 제한** (각 스크립트 1시간+ 실행 불가)
- 메모리 및 디스크 용량 제한
- 테라바이트급 데이터 처리 부적절

**Amazon Redshift 장점**:
- 페타바이트급 데이터 웨어하우스
- SQL 기반 분석 최적화

**Amazon Redshift 단점**:
- ETL 전용 서비스가 아님 (DW 서비스)
- Python 스크립트를 SQL로 완전 재작성 필요 (높은 개발 비용)
- 클러스터 관리 필요

## 🔍 주요개념

### 핵심 기술 비교

- **PySpark**: Python API for Apache Spark, 분산 데이터 처리 프레임워크
- **AWS Glue Python Shell**: 단일 노드 Python 실행 환경 (분산 처리 불가)
- **Lazy Evaluation**: 실제 액션이 호출될 때까지 변환 작업을 지연시키는 Spark의 최적화 기법

### 실전 적용

- **일일 배치 ETL**: 매일 테라바이트급 사용자 데이터를 정리하고 ML 피처로 변환
- **실시간 데이터 강화**: 외부 API나 참조 데이터와 조인하여 데이터 품질 향상
- **데이터 마이그레이션**: 레거시 시스템에서 현대적 데이터 레이크로 대규모 데이터 이전

## 📝 관련 문제

**Question:** A company is building a new recommendation engine. ML specialists need to process terabytes of user interaction data daily. The current Python scripts on EC2 take over 20 hours to complete, with each script requiring at least 1 hour. The company wants to move to a managed solution that eliminates server maintenance. Which approach addresses all requirements with the LEAST development effort?

**Options:**
- A) Load data into Amazon Redshift cluster. Execute pipeline using SQL. Store results in Amazon S3.
- B) Load data into Amazon DynamoDB. Convert scripts to AWS Lambda functions. Execute pipeline by triggering Lambda executions. Store results in Amazon S3.
- C) Create an AWS Glue job. Convert scripts to PySpark. Execute the pipeline. Store results in Amazon S3.
- D) Create individual AWS Lambda functions for each script. Build a step function using AWS Step Functions Data Science SDK. Store results in Amazon S3.

**정답: C) AWS Glue + PySpark**

💡 추가 설명:

- **Option A (Redshift + SQL)** - Python 스크립트를 SQL로 완전 재작성해야 하므로 높은 개발 비용, ETL보다는 DW 용도에 적합
- **Option B (DynamoDB + Lambda)** - Lambda의 15분 실행 시간 제한으로 1시간+ 스크립트 실행 불가능, DynamoDB는 배치 ETL에 부적절
- **Option D (Lambda + Step Functions)** - Lambda의 15분 제한 문제 동일, 복잡한 오케스트레이션으로 높은 개발 비용

## 🚀 AWS Glue의 위대함

이 문제는 사실상 **"AWS Glue 마케팅 문제"**로, 극단적인 시나리오(20시간, 테라바이트급)를 설정하여 Glue의 강점을 부각시키도록 설계되었습니다. 실제 환경에서는 AWS Batch, ECS/Fargate, EMR 등 다양한 대안이 존재하지만, 시험에서는 Glue가 유일한 정답이 되도록 다른 선택지들에 명백한 결함을 배치했습니다.

**현실 vs 시험**:
- 현실: 비용, 기존 인프라, 팀 역량을 고려한 다양한 솔루션 검토
- 시험: AWS가 홍보하고자 하는 서비스의 이상적 사용 사례 암기

결론적으로 이는 **"Glue 찬양 문제"**이며, AWS 시험의 전형적인 "영웅 서비스" 패턴을 보여주는 대표적인 사례입니다.