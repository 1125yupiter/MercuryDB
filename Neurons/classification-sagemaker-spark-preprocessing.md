---
title: classification-sagemaker-spark-preprocessing
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/classification
- service/sagemaker
- service/spark
- constraint/large-scale
- technique/preprocessing
aliases: ["video-classification", "libsvm-preprocessing", "spark-sagemaker"]
---

# 대규모 비디오 장르 분류를 위한 SageMaker와 XGBoost 데이터 전처리

## 🎯 핵심 포인트

수백만 개의 비디오를 장르별로 분류해야 하는 경우 Amazon SageMaker와 XGBoost에서, 대용량 데이터를 효율적으로 처리하기 위해 분산 처리가 가능한 Spark 기반 전처리 방법을 사용할 수 있다.

## 📝 설명

### Spark가 대규모 비디오 데이터 전처리에 적합한 이유

대규모 비디오 속성 데이터를 XGBoost 모델 학습을 위한 LibSVM 형식으로 변환할 때, 단일 노드 처리로는 성능과 확장성에 한계가 있다. Apache Spark는 분산 컴퓨팅 프레임워크로서 여러 노드에 걸쳐 데이터 처리를 병렬화할 수 있으며, 메모리 기반 처리를 통해 빠른 성능을 제공한다. 또한 sagemaker-spark 라이브러리를 통해 SageMaker와 원활한 통합이 가능하다.

### 아키텍처 플로우

```
비디오 속성 데이터 (S3)
    ↓
Spark 클러스터 (EMR 또는 SageMaker)
    ↓
데이터 정제 및 변환
    ↓
LibSVM 형식 변환
    ↓
전처리된 데이터 저장 (S3)
    ↓
SageMaker XGBoost 훈련
    ↓
모델 배포
```

### Trade-offs 고려사항

**Spark on EMR 장점**:
- 완전한 분산 처리 능력
- 대용량 데이터 처리에 최적화
- 유연한 클러스터 크기 조정
- LibSVM 형식 변환 지원

**Spark on EMR 단점**:
- 별도의 클러스터 관리 필요
- EMR과 SageMaker 간 데이터 이동 시간
- 추가적인 인프라 비용

**PySpark with XGBoostSageMakerEstimator 장점**:
- SageMaker 내에서 통합된 워크플로
- 데이터 전처리부터 훈련까지 원스톱
- sagemaker-spark 라이브러리 활용

**PySpark with XGBoostSageMakerEstimator 단점**:
- SageMaker 노트북 인스턴스 제약
- EMR 대비 제한된 분산 처리 능력

**scikit-learn 단점**:
- 단일 노드 처리로 확장성 부족
- 대용량 데이터 처리 시 성능 저하
- 메모리 제약으로 인한 처리 한계

**AWS Glue ETL 단점**:
- LibSVM 형식 네이티브 지원 부족
- XGBoost 특화 전처리 기능 제한

## 🔍 주요개념

### 데이터 전처리 방식 비교

- **분산 처리**: Spark 기반 솔루션으로 여러 노드에서 병렬 처리
- **단일 노드 처리**: scikit-learn과 같은 전통적 라이브러리 사용
- **실시간 처리**: Kinesis Analytics와 같은 스트리밍 처리
- **배치 처리**: EMR, Glue와 같은 대용량 배치 작업

### 실전 적용

- **미디어 플랫폼**: 수백만 개의 비디오 콘텐츠 자동 장르 분류
- **소셜 미디어**: 사용자 업로드 동영상의 실시간 카테고리 분류
- **교육 플랫폼**: 강의 영상의 주제별 자동 분류 및 추천

## 📝 관련 문제

**Question:** A media company needs to classify millions of videos into genres using Amazon SageMaker and XGBoost. The video attribute data requires cleaning and conversion to LibSVM format before model training. Which two approaches are most suitable for data preprocessing? (SELECT TWO)

**Options:**

- A) Use scikit-learn within a SageMaker notebook to preprocess the video attribute data
- B) Apply Spark on Amazon EMR for data preprocessing and save results in S3 for SageMaker access
- C) Deploy AWS Glue ETL to convert video attribute data into LibSVM format
- D) Use PySpark with XGBoostSageMakerEstimator for data preparation and training
- E) Employ Kinesis Analytics for real-time data transformation into LibSVM format

**정답: B, D) Spark on EMR + PySpark with XGBoostSageMakerEstimator**

💡 추가 설명:

- **옵션 A (scikit-learn)** - 단일 노드 처리로 수백만 개 비디오 데이터 처리에는 확장성 부족
- **옵션 C (AWS Glue ETL)** - LibSVM 형식 네이티브 지원 부족으로 XGBoost 전용 전처리에 부적합
- **옵션 E (Kinesis Analytics)** - 실시간 스트리밍 분석용으로 대용량 배치 전처리에는 부적합하며 LibSVM 형식 직접 지원 안함