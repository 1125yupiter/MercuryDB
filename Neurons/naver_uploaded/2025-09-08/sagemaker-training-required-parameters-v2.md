---
title: sagemaker-training-required-parameters
created: 2025-08-25
modified: 2025-08-25
tags:
- service/sagemaker
- training/parameters
- infrastructure/ec2
- deployment/managed
- requirement/mandatory
aliases: ["sagemaker-params", "training-params", "sm-training"]
---

# SageMaker 훈련 작업의 필수 파라미터와 EC2 기반 인프라

## 🎯 핵심 포인트

SageMaker에서 내장 알고리즘을 사용한 훈련 작업을 제출할 때 IAM 역할, 컴퓨팅 리소스 타입, 출력 경로 지정이 필수이며, 모든 작업은 EC2 인스턴스에서 실행되지만 자동 관리형과 지속형으로 구분된다.

## 📝 설명

### SageMaker가 훈련 작업에 적합한 이유

Amazon SageMaker는 완전 관리형 머신러닝 서비스로, 내부적으로 EC2 인스턴스를 자동으로 프로비저닝하고 관리합니다. 훈련 작업 실행 시 사용자가 EC2를 직접 생성하거나 관리할 필요 없이, 단순히 `estimator.fit()` 호출만으로 전체 훈련 파이프라인이 자동화됩니다.

### 아키텍처 플로우

```
사용자 코드 (estimator.fit())
    ↓
SageMaker API 호출
    ↓
EC2 인스턴스 자동 생성 (ml.p3.2xlarge 등)
    ↓
컨테이너 실행 + 훈련 알고리즘 로드
    ↓
S3에서 훈련 데이터 다운로드
    ↓
모델 훈련 실행
    ↓
훈련된 모델을 S3에 저장
    ↓
EC2 인스턴스 자동 종료 + 비용 과금 중단
```

### Trade-offs 고려사항

**자동 관리형 작업의 장점**:
- EC2 인스턴스 생성/삭제 자동화로 운영 부담 없음
- 훈련 완료 시 자동 종료로 비용 최적화
- 확장성과 안정성 보장

**자동 관리형 작업의 단점**:
- 실시간 디버깅이나 중간 개입 어려움
- 인스턴스 타입 변경 시 새로운 작업 시작 필요

**지속형 서비스의 장점**:
- 실시간 개발 및 디버깅 가능
- 즉시 응답이 필요한 서비스에 적합

**지속형 서비스의 단점**:
- 수동 관리 필요로 비용 최적화 어려움
- 24시간 실행으로 인한 높은 비용

## 🔍 주요개념

### 필수 파라미터 비교

- **IAM 역할 (RoleArn)**: SageMaker가 S3 접근, CloudWatch 로깅 등을 수행할 수 있는 권한
- **컴퓨팅 리소스 (InstanceType)**: 훈련에 사용할 EC2 인스턴스 타입 (CPU/GPU, 메모리, 네트워크 성능)
- **출력 경로 (OutputPath)**: 훈련된 모델 아티팩트가 저장될 S3 위치

### 실전 적용

- **대용량 딥러닝 모델 훈련**: GPU 인스턴스(ml.p3.8xlarge) + 분산 훈련으로 학습 시간 단축
- **비용 최적화 실험**: 작은 데이터셋은 CPU 인스턴스(ml.m5.large)로 비용 절감
- **프로덕션 파이프라인**: 자동 관리형 훈련 작업으로 안정성과 확장성 확보

## 📝 관련 문제

**Question:** Which common parameters MUST be given when submitting Amazon SageMaker training tasks that use one of the built-in algorithms? (Select three.)

**Options:**

- A) The training channel identifying the location of training data on an Amazon S3 bucket.
- B) The validation channel identifying the location of validation data on an Amazon S3 bucket.
- C) The IAM role that Amazon SageMaker can assume to perform tasks on behalf of the users.
- D) Hyperparameters in a JSON array as documented for the algorithm used.
- E) The Amazon EC2 instance class specifying whether training will be run using CPU or GPU.
- F) The output path specifying where on an Amazon S3 bucket the trained model will persist.

**정답: C, E, F**

💡 추가 설명:

- **A (훈련 채널)** - InputDataConfig는 Required: No로 표시된 선택적 매개변수. 실제로는 fit() 호출 시 데이터 제공
- **B (검증 채널)** - 모든 알고리즘이 검증 데이터를 요구하지 않는 선택적 매개변수
- **D (하이퍼파라미터)** - 대부분 내장 알고리즘이 기본값을 제공하는 선택적 매개변수
- **E의 용어 이슈** - "EC2 instance class"는 부정확한 표현이지만, 컴퓨팅 리소스 지정은 필수. SageMaker는 내부적으로 EC2 기반으로 동작