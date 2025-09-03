---

title: ml-pipeline-cloudwatch-monitoring-performance
created: 2025-08-19
modified: 2025-08-19
tags:
- service/cloudwatch
- problem/monitoring
- constraint/large-datasets
- usecase/ml-pipeline
- deployment/multi-service
aliases: ["cloudwatch-ml", "pipeline-monitor", "aws-monitoring"]

---

# ML 파이프라인 성능 모니터링을 위한 CloudWatch 활용

## 🎯 핵심 포인트

대규모 데이터셋을 처리하는 ML 파이프라인에서 여러 AWS 서비스(SageMaker, Glue, S3, Redshift)의 성능을 모니터링하고 이슈를 감지해야 하는 경우, Amazon CloudWatch를 통해 통합 모니터링과 실시간 알림을 구현할 수 있다.

## 📝 설명

### CloudWatch가 ML 파이프라인 모니터링에 적합한 이유

Amazon CloudWatch는 AWS 리소스와 애플리케이션을 모니터링하는 관찰 가능성 서비스로, ML 파이프라인의 모든 구성 요소를 중앙에서 관리할 수 있습니다. 각 서비스별 메트릭 수집, 로그 분석, 실시간 알람 설정을 통해 파이프라인의 전체적인 건강 상태를 파악하고 문제를 조기에 감지할 수 있습니다.

### 아키텍처 플로우

```
S3 (데이터 저장)
    ↓ [데이터 수집 메트릭]
Glue (ETL 처리)
    ↓ [처리 시간, 오류율]
SageMaker (모델 훈련/추론)
    ↓ [훈련 시간, 추론 지연시간]
Redshift (데이터 분석)
    ↓ [쿼리 성능, 클러스터 사용률]
CloudWatch (중앙 모니터링)
    ↓ [알람 트리거]
SNS/Lambda (자동 대응)
```

### Trade-offs 고려사항

**CloudWatch 장점**:
- AWS 서비스와의 네이티브 통합으로 설정이 간단
- 실시간 메트릭 수집과 알람 기능
- 커스텀 메트릭과 대시보드 구성 가능
- 로그 수집과 분석 도구 제공

**CloudWatch 단점**:
- 메트릭 저장 비용이 장기간 누적될 수 있음
- 복잡한 분석을 위해서는 추가 도구 필요
- 타사 서비스 모니터링에는 제한적

**Lambda 장점**:
- 서버리스 환경에서 커스텀 모니터링 로직 구현 가능

**Lambda 단점**:
- 전용 모니터링 서비스가 아니므로 기본 기능 부족
- 모니터링 로직을 직접 개발해야 함

**Kinesis 장점**:
- 실시간 스트리밍 데이터 처리에 특화

**Kinesis 단점**:
- 모니터링 도구가 아닌 데이터 처리 서비스
- 메트릭 수집 기능 없음

**QuickSight 장점**:
- 수집된 데이터의 시각화와 BI 분석에 강점

**QuickSight 단점**:
- 실시간 모니터링과 알람 기능 부족
- 메트릭 수집 기능 없음

## 🔍 주요개념

### 모니터링 유형 비교

- **인프라 모니터링**: 각 AWS 서비스의 리소스 사용률과 성능 지표
- **애플리케이션 모니터링**: ML 파이프라인의 비즈니스 로직과 처리 결과
- **로그 모니터링**: 오류 추적과 디버깅을 위한 로그 분석

### 실전 적용

- **실시간 알람**: 모델 추론 지연시간이 임계값 초과 시 즉시 알림
- **배치 처리 모니터링**: Glue ETL 작업의 실패율과 처리 시간 추적
- **데이터 품질 감시**: S3 데이터 수집량과 Redshift 쿼리 성능 모니터링

## 📝 관련 문제

**Question:** You are building a machine learning pipeline on AWS that processes large datasets. The pipeline consists of multiple AWS services, including Amazon SageMaker, AWS Glue, Amazon S3, and Amazon Redshift. You need to monitor the pipeline's performance and detect any issues that may arise. Which of the following services can help you achieve this?

**Options:**

- A) AWS Lambda
- B) Amazon Kinesis  
- C) Amazon CloudWatch
- D) Amazon QuickSight

**정답: C) Amazon CloudWatch**

💡 추가 설명:

- **AWS Lambda** - 서버리스 컴퓨팅 서비스로 모니터링 스크립트 실행은 가능하지만 전용 모니터링 도구가 아님
- **Amazon Kinesis** - 실시간 스트리밍 데이터 처리 플랫폼으로 모니터링 서비스가 아님  
- **Amazon QuickSight** - BI 서비스로 데이터 시각화에 특화되어 있지만 실시간 모니터링 기능은 제한적