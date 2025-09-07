---
title: sagemaker-custom-ecr-s3
created: 2025-08-24
modified: 2025-08-24
tags:
- service/sagemaker
- service/ecr
- service/s3
- deployment/custom-algorithm
- container/docker
aliases: ["sagemaker-custom", "ecr-sagemaker", "custom-ml-algorithm"]
---

# SageMaker 커스텀 알고리즘을 위한 ECR과 S3 활용

## 🎯 핵심 포인트

Amazon SageMaker에서 커스텀 ML 알고리즘을 구현하는 경우 ECR에서 컨테이너 이미지를 저장하고, S3에서 외부 자산과 매개변수를 관리할 수 있다.

## 📝 설명

### SageMaker 커스텀 알고리즘 아키텍처

SageMaker는 완전 관리형 머신러닝 플랫폼으로, 사용자가 직접 개발한 알고리즘을 컨테이너 기반으로 실행할 수 있습니다. 이때 두 가지 핵심 구성 요소가 필요합니다:

1. **알고리즘 코드**: Docker 컨테이너로 패키징하여 ECR에 저장
2. **외부 자산**: 훈련 데이터, 모델 파라미터, 설정 파일 등을 S3에 저장

### 아키텍처 플로우

```
개발자 코드 → Docker 이미지 빌드 → ECR 푸시
                                        ↓
훈련 데이터/파라미터 → S3 업로드 → SageMaker 훈련 작업 → 모델 아티팩트
                                        ↑
                            ECR에서 이미지 풀
```

### Trade-offs 고려사항

**ECR (Elastic Container Registry) 장점**:
- SageMaker와 완전 통합되어 원활한 이미지 관리
- 프라이빗 레지스트리로 보안성 확보
- 이미지 버전 관리 및 태깅 기능
- IAM 기반 세밀한 접근 제어

**ECR 단점**:
- Docker 이미지 빌드 및 관리 복잡성
- 이미지 크기에 따른 스토리지 비용

**S3 장점**:
- 대용량 데이터 저장에 최적화
- 99.999999999% (11 9's) 내구성
- 다양한 스토리지 클래스로 비용 최적화
- SageMaker와 네이티브 연동

**S3 단점**:
- 대용량 데이터 전송 시 네트워크 비용
- 빈번한 액세스 시 요청 비용 발생

**ECS가 부적합한 이유**:
- SageMaker 자체가 컨테이너 오케스트레이션 제공
- 별도의 클러스터 관리 불필요
- SageMaker의 관리형 인프라 사용으로 운영 복잡성 감소

## 🔍 주요개념

### ECR vs ECS 관계 이해

- **ECR (Elastic Container Registry)**: Docker 이미지 **저장소** - Docker Hub와 같은 역할
- **ECS (Elastic Container Service)**: 컨테이너 **실행 관리** - 배포, 스케일링, 오케스트레이션 담당

### 실전 적용

- **금융 사기 탐지**: 커스텀 앙상블 모델을 ECR에 저장, 거래 데이터를 S3에서 관리
- **의료 이미지 분석**: 특화된 CNN 모델 컨테이너화, 대용량 의료 이미지를 S3에 저장
- **자연어 처리**: 도메인 특화 BERT 모델을 컨테이너로 패키징, 텍스트 코퍼스를 S3에서 관리

## 📝 관련 문제

**Question:** A Machine Learning team on Amazon SageMaker runs its own training algorithm that requires external assets. The team must submit both algorithm code and algorithm-specific parameters to SageMaker. Which services should be combined to create a custom algorithm? (Select two.)

**Options:**
- A) AWS Secrets Manager
- B) AWS CodeStar  
- C) Amazon ECR
- D) Amazon ECS
- E) Amazon S3

**정답: C) Amazon ECR, E) Amazon S3**

💡 추가 설명:

- **AWS Secrets Manager** - 보안 정보 관리용으로 커스텀 알고리즘 생성에 직접 필요하지 않음
- **AWS CodeStar** - 개발 프로젝트 관리 도구로 SageMaker 알고리즘 구현에 필수가 아님  
- **Amazon ECS** - 컨테이너 오케스트레이션 서비스이지만 SageMaker가 자체적으로 컨테이너 관리를 제공
- **핵심**: ECR은 알고리즘 컨테이너 저장, S3는 외부 자산 및 매개변수 저장 역할