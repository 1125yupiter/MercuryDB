---

title: aws-batch-ml-service-selection
created: 2025-08-20
modified: 2025-08-20
tags:
- service/s3
- service/emr
- constraint/batch-processing
- usecase/machine-learning
- problem/service-selection
aliases: ["aws-batch-ml", "batch-processing-aws", "ml-batch-services"]

---

# AWS 배치 모드 머신러닝 데이터 처리 서비스 선택

## 🎯 핵심 포인트

머신러닝 목적으로 배치 모드에서 데이터를 수집하고 처리해야 하는 경우, Amazon S3에서 데이터를 저장하고 Amazon EMR에서 처리할 수 있다.

## 📝 설명

### 배치 모드 데이터 처리 아키텍처

머신러닝을 위한 배치 처리는 일반적으로 **데이터 저장소**와 **처리 엔진**의 조합으로 구성됩니다.

#### Amazon S3 (데이터 수집 및 저장)
- 대용량 데이터의 배치 수집과 저장을 위한 확장 가능한 객체 스토리지
- 다양한 데이터 타입과 형식 지원 (JSON, CSV, Parquet 등)
- 머신러닝을 위한 데이터 레이크 역할
- 외부 시스템으로부터 배치로 데이터 업로드 수용

#### Amazon EMR (데이터 처리)
- Apache Hadoop, Spark 등을 사용한 관리형 빅데이터 처리 서비스
- 대규모 데이터셋에서 분산 머신러닝 알고리즘 실행 가능
- 배치 처리에 최적화된 서비스
- S3에서 데이터를 읽어와 복잡한 변환 및 분석 수행

### 아키텍처 플로우

```
[외부 데이터 소스] → [Amazon S3] → [Amazon EMR] → [처리된 결과]
        ↓               ↓             ↓              ↓
   로그파일, DB        데이터 레이크    Spark 클러스터    ML 모델 학습
   API 데이터         배치 저장소      분산 처리         피처 스토어
```

### Trade-offs 고려사항

**Amazon S3 장점**:
- 무제한 확장성과 높은 내구성
- 다양한 데이터 형식 지원
- 저렴한 저장 비용
- 다른 AWS 서비스와의 원활한 통합

**Amazon S3 단점**:
- 자체적인 데이터 처리 기능 없음
- 컴퓨팅 리소스 제공하지 않음

**Amazon EMR 장점**:
- 완전한 제어권과 유연성
- 다양한 빅데이터 프레임워크 지원
- 복잡한 ML 파이프라인 구축 가능
- 비용 최적화 옵션 다양 (스팟 인스턴스 등)

**Amazon EMR 단점**:
- 클러스터 관리 필요
- 설정 복잡성
- 운영 오버헤드 존재

## 🔍 주요개념

### 배치 처리 vs 실시간 처리

- **배치 처리**: 대량 데이터를 주기적으로 일괄 처리 (S3 + EMR)
- **실시간 처리**: 데이터 스트림을 즉시 처리 (Kinesis, Lambda)

### 서비스 역할별 분류

- **저장소**: Amazon S3 (데이터 보관)
- **처리 엔진**: Amazon EMR (실제 데이터 변환/분석)
- **ETL 서비스**: AWS Glue (주로 배치 ETL, 스트리밍은 실시간)
- **스트리밍**: Kinesis Data Firehose (실시간 전송)
- **워크플로우**: Data Pipeline (데이터 이동, 대규모 처리에 부적합)

### 실전 적용

- **대규모 로그 분석**: S3에 저장된 TB급 로그를 EMR Spark로 분석
- **ML 데이터 준비**: S3의 원시 데이터를 EMR에서 피처 엔지니어링
- **배치 예측**: 학습된 모델을 EMR에서 대용량 데이터에 적용

## 📝 관련 문제

**Question:** A company needs to process terabytes of historical customer transaction data stored across multiple databases for machine learning model training. The data processing involves complex aggregations and feature engineering that must be completed in daily batch jobs. Which AWS services can be used for ingesting and processing this data in batch mode? (Select 2)

**Options:**

- A) Amazon S3
- B) AWS Glue  
- C) Amazon EMR
- D) Amazon Kinesis Data Firehose
- E) AWS Data Pipeline

**정답: A) Amazon S3, C) Amazon EMR**

💡 추가 설명:

- **AWS Glue** - ETL 서비스이지만 스트리밍 기능은 실시간 처리용이며, 배치 처리에서는 주로 이미 저장된 데이터의 변환에 특화
- **Amazon Kinesis Data Firehose** - 실시간 스트리밍 전송 서비스로 배치 처리와 무관
- **AWS Data Pipeline** - 워크플로우 관리 도구로 대규모 배치 처리에는 부적합

## 🔑 핵심 학습 포인트

### 문제 해석의 두 가지 패턴

AWS 시험에서 "Select 2 services" 문제는 두 가지 유형이 있습니다:

1. **각각 독립적 기능**: "Which services **can be used** for..." → 동일 목적을 달성하는 여러 서비스 중 선택
2. **조합 기능**: "Which **combination** of services..." → 함께 사용해야 하는 서비스들 선택

이 문제는 **조합 기능** 유형으로, ingesting(S3)과 processing(EMR)을 함께 수행하는 서비스 조합을 찾는 문제입니다.

### 서비스 분류법
1. **Storage Layer** (수집/보관): S3, RDS  
2. **Processing Layer** (변환/분석): EMR, Glue
3. **Transport Layer** (전송): Kinesis, Data Pipeline
4. **Compute Layer** (연산): Lambda, EC2