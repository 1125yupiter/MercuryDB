---

title: lambda-ml-cost-optimization-constraints
created: 2025-08-21
modified: 2025-08-21
tags:
- service/lambda
- optimization/cost
- constraint/memory-timeout
- usecase/ml-inference
- deployment/serverless
aliases: ["lambda-ml", "serverless-ml", "lambda-cost-opt"]

---

# AWS Lambda를 활용한 머신러닝 애플리케이션 비용 최적화 및 제약사항

## 🎯 핵심 포인트

가벼운 머신러닝 추론 작업의 경우 AWS Lambda에서 적절한 타임아웃 설정과 메모리 최적화를 통해 비용 효율적인 서버리스 ML 서비스를 구축할 수 있다.

## 📝 설명

### AWS Lambda가 머신러닝 워크로드에 적합한 이유

AWS Lambda는 서버리스 컴퓨팅 서비스로, 개발자가 서버 관리 없이 코드를 실행할 수 있는 환경을 제공합니다. Lambda는 Firecracker 기반 마이크로VM을 사용하여 필요할 때만 실행 환경을 생성하는 "이벤트 기반 마이크로 인스턴스" 방식으로 동작합니다.

**Lambda의 핵심 동작 방식:**
- 요청 발생 시 독립적인 실행 환경 생성
- 코드 실행 완료 후 환경 정리 (또는 재사용을 위해 잠시 유지)
- AWS가 모든 인프라를 자동 관리
- 실행 시간과 메모리 사용량에 따른 종량제 과금

### 아키텍처 플로우

```
이벤트 발생 (API Gateway, S3, DynamoDB 등)
    ↓
Lambda 마이크로 인스턴스 생성 (Cold Start)
    ↓
모델 로딩 (첫 실행시) + 추론 실행
    ↓
결과 반환
    ↓
환경 정리 또는 재사용을 위한 유지
```

### Trade-offs 고려사항

**Lambda 장점**:
- 서버 관리 불필요 (완전 서버리스)
- 사용한 만큼만 과금 (실행 시간 기준)
- 자동 스케일링 (동시 1000개+ 인스턴스)
- Cold Start 최적화 가능 (Provisioned Concurrency)

**Lambda 단점**:
- 실행 시간 제한 (최대 15분)
- 메모리 제한 (최대 10GB)
- GPU 지원 없음
- Cold Start 지연 시간

**ECS/Fargate 장점**:
- 더 큰 메모리와 CPU 지원
- 컨테이너 기반 배포
- 지속적 실행 가능

**ECS/Fargate 단점**:
- GPU 지원 없음 (Fargate)
- 서버 관리 필요 (ECS)
- 24/7 실행 시 비용 증가

**SageMaker 장점**:
- GPU 네이티브 지원
- ML 특화 기능 (A/B 테스트, 모니터링)
- 모델 레지스트리 및 배포 자동화

**SageMaker 단점**:
- 높은 비용 (전용 ML 인스턴스)
- 복잡한 설정
- 간단한 작업에는 과도한 기능

## 🔍 주요개념

### 비용 최적화 전략

- **타임아웃 설정**: 불필요한 긴 실행을 방지하여 비용 절약
- **메모리 최적화**: CPU 성능과 연동된 메모리 할당으로 실행시간 단축
- **Provisioned Concurrency**: Cold Start 제거로 일관된 성능 제공
- **모델 최적화**: 경량화된 모델 사용으로 리소스 효율성 증대

### 서비스 계층구조

- **Lambda**: 초경량 추론 (BERT-small, MobileNet)
- **ECS/Fargate**: 중간 크기 모델 (ResNet, YOLO)
- **SageMaker**: 대형 모델 및 GPU 필요 작업
- **EMR**: 대규모 데이터 처리 및 분산 ML

### 실전 적용

- **텍스트 분류 API**: 실시간 감정 분석, 스팸 필터링
- **이미지 분류**: 상품 카테고리 분류, 품질 검사
- **추천 시스템**: 실시간 개인화 추천 (경량 모델)

## 📝 관련 문제

**Question:** You are building a machine learning application that requires you to train a model and make predictions based on the input data. You have decided to use AWS Lambda to run your machine learning application. However, you are concerned about the cost of running your Lambda function. Which of the following options would be the best way to reduce the cost of running your Lambda function?

**Options:**

- A) Increase the memory allocation of your Lambda function
- B) Use the serverless framework to manage your Lambda function  
- C) Use the AWS Step Functions to orchestrate your Lambda function
- D) Reduce the timeout limit of your Lambda function
- E) Enable Provisioned Concurrency for consistent performance

**정답: D) Reduce the timeout limit of your Lambda function**

💡 추가 설명:

- **A) 메모리 할당 증가** - 메모리가 증가하면 CPU 성능은 향상되지만 기본적으로 시간당 비용이 증가하므로 비용 절감과는 반대 효과
- **B) Serverless Framework 사용** - 배포 및 관리 도구일 뿐 비용 절감과는 직접적인 관련이 없음
- **C) AWS Step Functions 활용** - 워크플로우 오케스트레이션 기능을 제공하지만 오히려 추가 비용이 발생
- **E) Provisioned Concurrency 활성화** - 성능은 향상되지만 상시 대기 인스턴스로 인해 비용이 증가