---

title: dynamodb-sagemaker-training-pipeline
created: 2025-08-17
modified: 2025-08-17
tags:
- service/dynamodb
- service/sagemaker
- service/datapipeline
- usecase/recommendation
- constraint/batch-processing
aliases: ["dynamodb-ml", "sagemaker-training", "data-pipeline"]

---

# DynamoDB 데이터를 활용한 SageMaker 머신러닝 모델 훈련

## 🎯 핵심 포인트

DynamoDB에 저장된 사용자 인터랙션 데이터를 머신러닝 모델 훈련에 활용하고자 하는 경우, AWS Data Pipeline을 통해 데이터를 S3로 내보낸 후 SageMaker에서 훈련할 수 있다.

## 📝 설명

### SageMaker가 DynamoDB 직접 연결 대신 S3를 요구하는 이유

SageMaker는 훈련 작업 시 **Amazon S3만을 직접 데이터 소스로 지원**합니다. 이는 다음과 같은 이유 때문입니다:

- **확장성**: 대용량 데이터셋의 효율적 처리
- **내구성**: 훈련 중 데이터 손실 방지
- **성능**: 병렬 처리와 분산 훈련 최적화
- **표준화**: 일관된 데이터 포맷(CSV, JSON, Parquet 등) 지원

### 아키텍처 플로우

```
DynamoDB Table (User Interactions)
         ↓
   AWS Data Pipeline
         ↓
   Amazon S3 (JSON/CSV)
         ↓
   SageMaker Training Job
         ↓
   Model Artifact (S3)
         ↓
   SageMaker Endpoint
```

### 상세 프로세스

1. **데이터 수집**: 웹사이트 사용자 인터랙션을 DynamoDB에 실시간 저장
2. **데이터 내보내기**: AWS Data Pipeline이 스케줄에 따라 DynamoDB 전체 내용을 JSON으로 S3에 복사
3. **데이터 변환**: JSON 파일을 SageMaker 호환 CSV 형태로 변환
4. **모델 훈련**: SageMaker가 S3의 CSV 데이터로 추천 모델 훈련
5. **모델 배포**: 훈련된 모델을 SageMaker 엔드포인트로 배포
6. **주기적 갱신**: Data Pipeline이 정기적으로 이 과정을 반복

### Trade-offs 고려사항

**AWS Data Pipeline + S3 장점**:
- SageMaker와 완벽 호환
- 대용량 데이터 처리 최적화
- 스케줄 기반 자동화 가능
- Athena 등 다른 분석 도구와 연계 활용
- CSV 변환으로 다양한 ML 프레임워크 지원

**AWS Data Pipeline + S3 단점**:
- 실시간 훈련 불가능 (배치 처리)
- 추가 스토리지 비용 발생
- 데이터 변환 과정 필요

**DynamoDB 직접 연결의 한계**:
- SageMaker가 DynamoDB 직접 액세스 미지원
- NoSQL 구조가 ML 훈련에 비효율적
- 스캔 작업이 DynamoDB 성능에 영향

**DMS + RDS 대안의 단점**:
- SageMaker가 RDS 직접 액세스도 미지원
- 불필요한 아키텍처 복잡성 증가
- 추가 인프라 관리 부담

**DynamoDB Streams의 한계**:
- 스트림 엔드포인트는 SageMaker 데이터 소스 불가
- 실시간 스트림은 배치 훈련에 부적합

## 🔍 주요개념

### 데이터 파이프라인 vs 실시간 스트림

- **Data Pipeline**: 정기적 배치 처리로 전체 데이터셋 동기화, 모델 재훈련에 적합
- **DynamoDB Streams**: 실시간 변경 캡처, 온라인 피처 스토어나 실시간 추론에 적합

### 실전 적용

- **전자상거래 추천**: 사용자 구매/조회 히스토리로 상품 추천 모델 훈련
- **컨텐츠 개인화**: 사용자 클릭/시청 패턴으로 컨텐츠 추천 시스템 구축  
- **고객 이탈 예측**: 사용자 행동 데이터로 이탈 가능성 예측 모델 개발

## 📝 관련 문제

**Question:** A company uses Amazon DynamoDB to store user-interaction data generated from a website. A Machine Learning Specialist was instructed to use this data in designing a product recommendation system. Which approach will enable the Specialist to use Amazon SageMaker AI to train a model?

**Options:**

- A) Use AWS DMS to migrate the DynamoDB table data into a MySQL database hosted on Amazon RDS. Provide the new database endpoint within the notebook instance.
- B) Enable DynamoDB Streams to copy the data into a new table and provide the DynamoDB Stream endpoint within the notebook instance.
- C) Pull the data from the database by configuring a direct connection between the notebook instance and the DynamoDB table.
- D) Export the DynamoDB table data into an Amazon S3 bucket using AWS Data Pipeline and provide the S3 path of the data within the notebook instance.

**정답: D) AWS Data Pipeline을 사용해 DynamoDB 테이블 데이터를 Amazon S3 버킷으로 내보내고 노트북 인스턴스에서 S3 경로 제공**

💡 추가 설명:

- **A) DMS + RDS 옵션** - SageMaker는 RDS에 직접 연결하여 데이터를 읽을 수 없으며, 모든 훈련 데이터는 S3를 통해 제공되어야 함
- **B) DynamoDB Streams 옵션** - Streams는 실시간 변경 캡처용이며, SageMaker 노트북에서 스트림 엔드포인트를 데이터 소스로 사용할 수 없음
- **C) 직접 연결 옵션** - SageMaker와 DynamoDB 간 직접 연결은 지원되지 않으며, 모든 훈련 데이터는 반드시 S3를 거쳐야 함