---
title: sagemaker-training-required-parameters
created: 2025-08-22
modified: 2025-08-22
tags:
- service/sagemaker
- operation/training
- config/required
- parameters/core
- aws/api
aliases: ["sagemaker-training", "training-params", "required-config"]

---

# Amazon SageMaker 훈련 작업 필수 파라미터

## 🎯 핵심 포인트

SageMaker 훈련 작업을 생성할 경우 API에서, IAM 역할(권한), 리소스 구성(인스턴스), 출력 경로(결과 저장소) 세 가지 파라미터가 반드시 필요하다.

## 📝 설명

### SageMaker가 훈련 작업 수행에 적합한 이유

Amazon SageMaker는 완전 관리형 머신러닝 서비스로, 복잡한 인프라 관리 없이 모델 훈련을 수행할 수 있다. CreateTrainingJob API는 훈련 작업의 모든 측면을 정의하며, 이때 최소한의 필수 파라미터로 작업을 시작할 수 있다.

### 아키텍처 플로우

```
사용자 요청 → CreateTrainingJob API
    ↓
필수 파라미터 검증
    ↓
RoleArn (권한) → S3/ECR 접근 권한 확인
    ↓
ResourceConfig (리소스) → EC2 인스턴스 프로비저닝
    ↓
OutputDataConfig (출력) → 모델 저장 경로 설정
    ↓
훈련 작업 실행 → 모델 아티팩트 저장
```

### Trade-offs 고려사항

**필수 파라미터의 장점**:
- 최소한의 설정으로 빠른 시작 가능
- 기본값 제공으로 복잡성 감소
- 명확한 권한과 리소스 분리

**필수 파라미터의 제약**:
- 유연성 제한 (기본 설정에 의존)
- 고급 기능 사용시 추가 설정 필요

**선택적 파라미터의 장점**:
- 세밀한 커스터마이징 가능
- 다양한 알고리즘별 최적화

**선택적 파라미터의 단점**:
- 설정 복잡도 증가
- 잘못된 설정시 성능 저하 위험

## 🔍 주요개념

### 필수 vs 선택적 파라미터 구분

- **훈련 작업 파라미터**: SageMaker 서비스 레벨에서 작업 실행을 위한 설정
- **하이퍼파라미터**: 알고리즘 레벨에서 모델 학습을 제어하는 설정 (선택적)

### 3가지 핵심 질문

- **누가(권한)**: RoleArn - SageMaker가 어떤 권한으로 작업할 것인가?
- **어디서(리소스)**: ResourceConfig - 어떤 컴퓨팅 리소스를 사용할 것인가?
- **어디에(출력)**: OutputDataConfig - 결과를 어디에 저장할 것인가?

### 실전 적용

- 개발 환경에서 빠른 프로토타이핑시 최소 설정 활용
- 프로덕션 환경에서 세밀한 리소스 최적화를 위한 ResourceConfig 조정
- 멀티 리전 배포시 각 리전별 IAM 역할과 S3 버킷 설정

## 📝 관련 문제

**Question:** Which common parameters MUST be given when submitting Amazon SageMaker training tasks that use one of the built-in algorithms? (Select three.)

**Options:**

- A) The training channel identifying the location of training data on an Amazon S3 bucket
- B) The validation channel identifying the location of validation data on an Amazon S3 bucket  
- C) The IAM role that Amazon SageMaker can assume to perform tasks on behalf of the users
- D) Hyperparameters in a JSON array as documented for the algorithm used
- E) The Amazon EC2 instance class specifying whether training will be run using CPU or GPU
- F) The output path specifying where on an Amazon S3 bucket the trained model will persist

**정답: C, E, F) IAM role, EC2 instance configuration, Output path**

💡 추가 설명:

- **A (Training channel)** - InputDataConfig는 Required: No로, 많은 알고리즘이 기본 데이터셋을 제공
- **B (Validation channel)** - InputDataConfig의 일부로 선택사항이며, 검증 없이도 훈련 가능
- **D (Hyperparameters)** - 모든 내장 알고리즘이 기본값을 가지고 있어 Required: No
- **핵심 구분**: 서비스 레벨 설정(필수) vs 알고리즘 레벨 설정(선택)