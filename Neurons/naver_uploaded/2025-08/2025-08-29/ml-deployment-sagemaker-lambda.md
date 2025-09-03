---

title: ml-deployment-sagemaker-lambda
created: 2025-08-20
modified: 2025-08-20
tags:
- service/sagemaker
- service/lambda
- deployment/production
- constraint/model-size
- usecase/ml-inference
aliases: ["ML deployment", "model serving", "inference API"]

---

# 머신러닝 모델 배포: SageMaker vs Lambda 선택 가이드

## 🎯 핵심 포인트

프로덕션 환경에서 커스텀 ML 알고리즘을 배포할 경우 Amazon SageMaker에서 엔터프라이즈급 ML 워크플로우와 자동 스케일링을 활용할 수 있다.

## 📝 설명

### Amazon SageMaker가 커스텀 ML 알고리즘 배포에 적합한 이유

SageMaker는 머신러닝 모델의 전체 라이프사이클을 관리하는 완전 관리형 플랫폼입니다. Docker 컨테이너를 통한 BYOC(Bring Your Own Container) 기능으로 Python으로 구현한 커스텀 알고리즘을 쉽게 패키징하고 배포할 수 있습니다. 자동 스케일링, A/B 테스팅, 모델 모니터링 등 프로덕션 환경에 필수적인 기능들을 제공하며, RESTful API 엔드포인트를 통해 다른 시스템과의 통합이 용이합니다.

### 아키텍처 플로우

```
[Python 알고리즘] → [Docker 컨테이너화] → [SageMaker 모델 생성]
                                              ↓
[클라이언트 애플리케이션] ← [API Gateway] ← [SageMaker 엔드포인트]
                                              ↓
                                    [CloudWatch 모니터링]
```

### Trade-offs 고려사항

**SageMaker 장점**:
- ML 특화 기능 (모델 버전 관리, A/B 테스팅, 데이터 드리프트 모니터링)
- 대용량 모델 지원 및 GPU 인스턴스 활용 가능
- 일정한 응답 시간과 높은 처리량
- 엔터프라이즈급 보안 및 규정 준수

**SageMaker 단점**:
- 지속적인 인스턴스 비용 발생
- 설정 및 관리 복잡도 상대적으로 높음
- 최소 인스턴스 유지 필요

**Lambda 장점**:
- 완전 서버리스 (인프라 관리 불필요)
- 사용량 기반 과금으로 저트래픽 시 비용 효율적
- 빠른 배포 및 확장
- 이벤트 기반 아키텍처와 자연스러운 통합

**Lambda 단점**:
- 실행 시간 제한 (최대 15분)
- 메모리 제한 (최대 10GB)
- 콜드 스타트로 인한 초기 지연
- 대용량 모델 배포 어려움

## 🔍 주요개념

### 서비스별 적용 시나리오

- **SageMaker**: 복잡한 딥러닝 모델, 높은 트래픽, 실시간 추론, A/B 테스팅 필요
- **Lambda**: 간단한 모델 (<250MB), 낮은 트래픽, 서버리스 아키텍처 일부

### 실전 적용

- **전자상거래 추천 시스템**: SageMaker로 복잡한 협업 필터링 모델 배포
- **문서 감정 분석**: Lambda로 경량 NLP 모델 서빙
- **이미지 분류 API**: SageMaker로 CNN 모델을 통한 실시간 이미지 분류

## 📝 관련 문제

**Question:** You are working on a machine learning project that requires the use of a custom algorithm. You have implemented the algorithm in Python and want to deploy it on AWS. Which service would be the best choice for deploying a custom algorithm for use in a production environment?

**Options:**

- A) AWS Batch
- B) Amazon SageMaker  
- C) AWS Elastic Beanstalk
- D) AWS Lambda
- E) Amazon EC2

**정답: B) Amazon SageMaker**

💡 추가 설명:

- **AWS Batch** - 배치 작업용 서비스로 실시간 추론 API 제공에 부적합
- **AWS Elastic Beanstalk** - 일반 웹 애플리케이션용으로 ML 특화 기능 부족
- **AWS Lambda** - 메모리/시간 제한으로 복잡한 ML 모델 배포에 제약
- **Amazon EC2** - 직접 인프라 관리 필요로 운영 복잡도 증가