---

title: anomaly-kinesis-realtime-subway
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/anomaly-detection
- service/kinesis-analytics
- constraint/real-time
- algorithm/random-cut-forest
- usecase/transportation
aliases: ["subway-anomaly", "rcf-kinesis", "real-time-anomaly"]

---

# 지하철 승객 데이터 실시간 이상 감지 시스템

## 🎯 핵심 포인트

분 단위로 수집되는 지하철 승객 데이터에서 이상치를 실시간으로 감지해야 하는 경우, Amazon Kinesis Data Firehose로 데이터를 수집하고 Kinesis Data Analytics의 Random Cut Forest 알고리즘을 활용하여 자동 알림 시스템을 구축할 수 있다.

## 📝 설명

### Kinesis Data Analytics가 실시간 이상 감지에 적합한 이유

Amazon Kinesis Data Analytics는 스트리밍 데이터를 실시간으로 처리하는 완전 관리형 서비스입니다. 특히 Random Cut Forest(RCF) 알고리즘을 내장하여 시계열 데이터의 이상치를 자동으로 감지할 수 있습니다. 지하철 승객 데이터처럼 시간대별 패턴이 예측 가능한 데이터에서 갑작스러운 변화(사고, 이벤트, 시설 장애 등)를 빠르게 포착하는 데 최적화되어 있습니다.

### 아키텍처 플로우

```
지하철 시스템 → Kinesis Data Firehose → Kinesis Data Analytics (RCF) → Lambda → SNS → 알림
    (분당 승객수)     (데이터 수집)        (이상 감지)         (처리)  (발송) (관리자)
```

### Trade-offs 고려사항

**Kinesis Data Analytics + RCF 장점**:
- 실시간 스트림 처리로 지연 시간 최소화
- Random Cut Forest 알고리즘으로 정확한 이상치 감지
- 완전 관리형 서비스로 운영 부담 감소
- 서버리스 아키텍처로 비용 효율성

**Kinesis Data Analytics + RCF 단점**:
- 초기 학습 기간 필요 (패턴 인식을 위한 데이터 축적)
- 복잡한 비즈니스 로직 적용 시 제한적

**SageMaker 실시간 엔드포인트 장점**:
- 커스텀 머신러닝 모델 사용 가능
- 복잡한 알고리즘 적용 가능

**SageMaker 실시간 엔드포인트 단점**:
- 높은 비용 (24/7 인스턴스 실행)
- 복잡한 설정과 관리 필요
- 스트리밍 데이터 처리를 위한 추가 구성 요구

**CloudWatch 메트릭 기반 알림 장점**:
- 간단한 설정
- 저렴한 비용

**CloudWatch 메트릭 기반 알림 단점**:
- 단순 임계값 기반으로 정교한 이상 감지 어려움
- 시계열 패턴 학습 불가

## 🔍 주요개념

### 이상 감지 알고리즘 비교

- **Random Cut Forest (RCF)**: AWS에서 개발한 무지도 학습 알고리즘으로 시계열 데이터의 이상치를 효과적으로 감지. 특히 주기적 패턴이 있는 데이터에 강점
- **Statistical Threshold**: 평균과 표준편차를 기준으로 한 임계값 설정. 단순하지만 동적 패턴 변화에 대응 어려움

### 실전 적용

- **출퇴근 시간대 승객 급증/급감 감지**: 평소 패턴 대비 비정상적인 변화 포착
- **역별 승객 수 이상 변화 모니터링**: 특정 역에서 발생하는 돌발 상황 감지
- **시간대별 패턴 학습 기반 예측**: 요일별, 시간대별 정상 범위 자동 설정

## 📝 관련 문제

**Question:** Your organization is ingesting a data feed of subway ridership in near-real-time, where incoming data is timestamped by the minute and includes the total number of riders at each station for that minute. What is the simplest and most efficient approach for automatically sending alerts when an unusually high or low number of riders is observed?

**Options:**

- A) Ingest the data with Amazon Kinesis Data Firehose, utilize Random Cut Forest (RCF) in Amazon Kinesis Data Analytics for anomaly detection, and employ AWS Lambda to process the output from Kinesis Data Analytics, issuing an alert via Amazon SNS if needed
- B) Publish data directly into Amazon S3, and use AWS Glue to detect anomalies, then pass on alerts to Amazon SNS
- C) Ingest the data with Amazon Kinesis Firehose, and use Amazon CloudWatch to alert when anomalous data is detected
- D) Ingest the data with Amazon Kinesis Data Streams directly into Amazon S3, use Random Cut Forest in Amazon SageMaker to detect anomalies in real-time, and integrate SageMaker with Amazon SNS to issue alarms

**정답: A) Kinesis Data Firehose + Kinesis Data Analytics (RCF) + Lambda + SNS**

💡 추가 설명:

- **옵션 B (S3 + Glue)** - 배치 처리 방식으로 실시간 요구사항 충족 불가, 분 단위 알림에 부적합
- **옵션 C (CloudWatch 알림)** - 단순 임계값 기반으로 시계열 패턴 학습 및 정교한 이상 감지 어려움
- **옵션 D (SageMaker 실시간)** - 높은 비용과 복잡한 설정으로 단순하고 효율적인 솔루션 요구사항에 부적합