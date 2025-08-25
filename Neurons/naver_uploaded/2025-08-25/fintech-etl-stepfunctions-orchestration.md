---

title: fintech-etl-stepfunctions-orchestration
created: 2025-08-18
modified: 2025-08-18
tags:
- service/stepfunctions
- usecase/ml-pipeline
- constraint/dependency-management
- industry/fintech
- workflow/orchestration
aliases: ["step-functions-etl", "ml-pipeline-orchestration", "etl-workflow"]

---

# 핀테크 ETL 파이프라인 오케스트레이션 - AWS Step Functions

## 🎯 핵심 포인트

종속성 있는 야간 머신러닝 ETL 파이프라인의 경우 AWS Step Functions에서, 서버리스 워크플로우 오케스트레이션으로 자동화된 시퀀싱과 에러 핸들링을 구현할 수 있다.

## 📝 설명

### AWS Step Functions가 ETL 파이프라인 오케스트레이션에 적합한 이유

AWS Step Functions는 상태 머신(State Machine) 기반의 서버리스 워크플로우 오케스트레이션 서비스로, 복잡한 종속성을 가진 ETL 파이프라인에 최적화되어 있다. JSON 기반의 Amazon States Language(ASL)를 통해 워크플로우를 정의하며, 시각적 워크플로우 편집기를 제공한다. 핀테크 환경에서는 데이터 처리의 정확성과 규정 준수가 중요하기 때문에, Step Functions의 내장 오류 처리, 재시도 메커니즘, 완전한 감사 추적 기능이 특히 유용하다.

### 아키텍처 플로우

```
Data Sources → AWS Step Functions State Machine
                    ↓
              Data Extraction (Lambda/Glue)
                    ↓
              Data Validation & Cleaning
                    ↓
              Data Transformation (Glue ETL)
                    ↓
              Feature Engineering
                    ↓
              Model Training (SageMaker)
                    ↓
              Model Evaluation & Validation
                    ↓
              Model Deployment (SageMaker Endpoints)
                    ↓
              Notification & Monitoring (SNS/CloudWatch)
```

### Trade-offs 고려사항

**AWS Step Functions 장점**:
- 시각적 워크플로우 관리로 복잡한 의존성 직관적 파악
- 서버리스 아키텍처로 인프라 관리 부담 없음
- 내장 오류 처리 및 재시도 메커니즘 제공
- AWS 서비스와의 네이티브 통합 (Lambda, Glue, SageMaker 등)
- 완전한 실행 히스토리와 감사 추적
- 병렬 실행과 조건부 분기 지원

**AWS Step Functions 단점**:
- 상태 전환당 과금으로 복잡한 워크플로우에서 비용 증가
- ASL(Amazon States Language) 학습 곡선 필요
- 상태 머신당 최대 25,000개 실행 히스토리 제한

**Amazon MWAA 장점**:
- Apache Airflow의 강력한 기능과 플러그인 생태계
- Python 기반의 DAG 정의로 높은 유연성
- 복잡한 조건부 로직과 동적 워크플로우 구성 가능

**Amazon MWAA 단점**:
- 관리형 서비스지만 여전히 인프라 관리 필요
- 높은 운영 비용 (최소 환경도 시간당 과금)
- Apache Airflow 전문 지식 필요

**SQS + Lambda 조합 장점**:
- 높은 사용자 정의 가능성
- 세밀한 제어와 특수 로직 구현 가능
- 서비스별 독립적인 스케일링

**SQS + Lambda 조합 단점**:
- 복잡한 시퀀싱 로직을 직접 구현해야 함
- 상태 관리와 오류 처리 로직 개발 필요
- 워크플로우 가시성과 모니터링 구현 복잡

## 🔍 주요개념

### 핵심 워크플로우 패턴 비교

- **Sequential Processing**: 순차적 ETL 단계 실행으로 데이터 일관성 보장
- **Parallel Processing**: 독립적인 작업의 동시 실행으로 처리 시간 단축
- **Choice State**: 조건부 분기를 통한 데이터 품질 기반 라우팅
- **Wait State**: 스케줄링된 실행과 리소스 대기 시간 관리

### 실전 적용

- **야간 배치 처리**: 거래 데이터 정제 → 피처 엔지니어링 → 신용 위험 모델 훈련의 순차적 파이프라인
- **실시간 스트림 보강**: Kinesis 데이터 수집 → Lambda 전처리 → 실시간 이상 거래 탐지 모델 추론
- **규정 준수 리포팅**: 다중 데이터 소스 통합 → 규제 요구사항 검증 → 자동 보고서 생성 및 제출

## 📝 관련 문제

**Question:** A fintech startup is looking to streamline its nightly machine learning model training process, which involves a series of dependent ETL tasks. The goal is to find an approach that sequences these tasks efficiently, manages dependencies, and handles potential errors automatically. Which AWS service and feature combination best aligns with these requirements?

**Options:**

- A) Use AWS Step Functions' workflow orchestration to manage the sequence of tasks
- B) Apply Amazon SQS for task decoupling, using AWS Lambda for custom task sequencing and error management
- C) Implement Managed Workflows for Apache Airflow (MWAA) for flexible workflow orchestration
- D) Leverage AWS Batch's job scheduling capabilities for executing batch jobs

**정답: A) AWS Step Functions' workflow orchestration to manage the sequence of tasks**

💡 추가 설명:

- **Option B (SQS + Lambda)** - 높은 커스터마이징이 가능하지만 복잡한 워크플로우 시퀀싱과 에러 핸들링을 직접 구현해야 하는 개발 오버헤드 발생
- **Option C (MWAA)** - Apache Airflow의 강력한 기능을 제공하지만 학습 곡선이 가파르고 관리 비용이 높아 단순한 ETL 파이프라인에는 과도한 솔루션
- **Option D (AWS Batch)** - 배치 작업 실행에는 우수하지만 워크플로우 오케스트레이션 기능이 제한적이어서 별도 오케스트레이션 도구와 함께 사용해야 함