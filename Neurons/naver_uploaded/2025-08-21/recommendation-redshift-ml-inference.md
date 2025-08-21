---

title: recommendation-redshift-ml-inference
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/recommendation
- service/redshift-ml
- constraint/cost-optimization
- technique/in-database-ml
- usecase/job-matching
aliases: ["redshift-ml", "in-database-inference", "job-recommendation"]

---

# Redshift ML을 활용한 추천 시스템 비용 최적화

## 🎯 핵심 포인트

추천 시스템에서 데이터 이동 비용과 추론 지연시간을 줄이고자 하는 경우 Amazon Redshift에서, Redshift ML을 통해 데이터베이스 내 직접 추론을 수행할 수 있다.

## 📝 설명

### Redshift ML이 추천 시스템에 적합한 이유

Amazon Redshift ML은 데이터 웨어하우스 내에서 직접 기계학습 모델을 생성, 훈련, 적용할 수 있게 해주는 서비스입니다. 기존의 복잡한 데이터 파이프라인을 단순화하여 SQL 명령어만으로 머신러닝을 수행할 수 있습니다.

특히 추천 시스템에서는 대량의 사용자-아이템 상호작용 데이터가 이미 데이터 웨어하우스에 저장되어 있는 경우가 많아, 데이터를 별도 서비스로 이동시키지 않고 현재 위치에서 바로 처리할 수 있는 장점이 큽니다.

### 아키텍처 플로우

```
기존 아키텍처:
Redshift → Feature Extraction → CSV Export → S3 Storage → SageMaker Instance → Inference

최적화된 아키텍처:
Redshift → Redshift ML Model Training → In-Database SQL Inference
```

### Trade-offs 고려사항

**Redshift ML 장점**:
- 데이터 이동 비용 제거 (ETL 오버헤드 없음)
- SQL 기반 간단한 인터페이스
- 실시간 추론 지연시간 단축
- 인프라 관리 복잡도 감소
- 기존 BI 도구와의 자연스러운 통합

**Redshift ML 단점**:
- 복잡한 딥러닝 모델 지원 제한
- 모델 버전 관리의 어려움
- SageMaker 대비 제한적인 알고리즘 선택
- 대규모 실시간 추론 시 클러스터 성능 영향

**SageMaker 장점**:
- 다양한 알고리즘과 프레임워크 지원
- 전용 추론 인스턴스로 안정적 성능
- A/B 테스팅과 카나리 배포 지원
- 모델 모니터링과 관리 기능

**SageMaker 단점**:
- 데이터 이동으로 인한 지연시간과 비용
- 별도 인프라 관리 필요
- ETL 파이프라인 복잡도 증가

## 🔍 주요개념

### 핵심 개념 비교

- **In-Database ML**: 데이터가 위치한 곳에서 직접 머신러닝을 수행하는 방식으로, 데이터 이동 없이 SQL로 모델 훈련과 추론 실행
- **Serverless Inference**: 요청이 있을 때만 컴퓨팅 자원을 사용하는 방식이지만, 여전히 데이터 이동과 콜드 스타트 지연 문제 존재

### 실전 적용

- 고객 이탈 예측 모델을 Redshift 내 고객 데이터로 훈련하여 마케팅 대시보드에서 직접 활용
- 상품 추천 시스템에서 사용자 구매 이력을 기반으로 실시간 추천 점수 계산
- 직업 매칭 플랫폼에서 구직자 프로필과 채용공고 간 적합도 점수를 SQL 쿼리로 즉시 생성

## 📝 관련 문제

**Question:** A job search company has a recommender system that helps users find relevant job openings efficiently. The company uses a data pipeline that extracts input features from a Redshift cluster and saves it as CSV files in Amazon S3. The exported data is then fed into an Amazon SageMaker instance to make inferences. The company aims to lower inference costs and expedite the process of generating predictions. Which solution best meets the requirements?

**Options:**

- A) Use Amazon Redshift Streaming ingestion to feed inference data directly to the SageMaker instance
- B) Use Amazon Redshift ML to create a recommender system model and perform in-database local inference
- C) Attach an Amazon Elastic Inference (EI) accelerator to the SageMaker instance
- D) Convert the inference data from CSV into the Protobuf RecordIO format. Use Amazon SageMaker Serverless Inference to generate predictions

**정답: B) Use Amazon Redshift ML to create a recommender system model and perform in-database local inference**

💡 추가 설명:

- **Option A** - Redshift Streaming ingestion은 Kinesis나 Kafka 같은 스트리밍 소스에서 데이터를 수집하는 기능으로, SageMaker로 추론 요청을 보내는 용도가 아님
- **Option C** - Elastic Inference 가속기는 SageMaker 성능은 개선하지만 데이터 이동 비용과 지연시간 문제는 해결하지 못함
- **Option D** - 데이터 포맷 변환이 반드시 성능 향상을 보장하지 않으며, Serverless Inference도 데이터 파이프라인 비효율성은 해결하지 못함