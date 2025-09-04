---

title: sagemaker-inference-docker-ecr
created: 2025-08-23
modified: 2025-08-23
tags:
- service/sagemaker
- deployment/inference
- container/docker
- registry/ecr
- hosting/production
aliases: ["sagemaker-hosting", "docker-ecr", "inference-deployment"]

---

# SageMaker 추론을 위한 Docker 이미지 ECR 배포

## 🎯 핵심 포인트

로컬에서 훈련된 scikit-learn 모델을 SageMaker에서 호스팅하는 경우 Amazon ECR에서, Docker 이미지 빌드 → ECR 태깅 → 업로드 순서로 배포할 수 있다.

## 📝 설명

### Amazon SageMaker가 커스텀 모델 호스팅에 적합한 이유

SageMaker는 AWS의 완전 관리형 ML 플랫폼으로, 커스텀 추론 코드를 Docker 컨테이너로 패키징하여 배포할 수 있습니다. 보안과 통합성을 위해 Amazon ECR(Elastic Container Registry)만을 지원하며, Docker Hub 등 외부 레지스트리는 사용할 수 없습니다.

### 아키텍처 플로우

```
로컬 환경
└── scikit-learn 모델 훈련
    └── Docker 이미지 빌드 (추론 코드 + 모델 포함)
        └── ECR 레지스트리 주소로 태깅
            └── Amazon ECR에 푸시
                └── SageMaker 엔드포인트 생성
                    └── 실시간 추론 서비스
```

### Trade-offs 고려사항

**Amazon ECR 장점**:
- AWS 서비스와 완전 통합 (IAM, VPC 등)
- SageMaker에서 직접 지원
- 높은 보안성 및 접근 제어
- AWS 내 네트워크 최적화

**Amazon ECR 단점**:
- AWS 종속성
- Docker Hub 대비 상대적으로 높은 비용
- 초기 설정 복잡성

**Docker Hub 장점**:
- 무료 공개 저장소
- 대규모 커뮤니티와 이미지 생태계
- 간단한 사용법

**Docker Hub 단점**:
- SageMaker에서 지원하지 않음
- AWS 보안 정책과 미호환
- 엔터프라이즈 보안 기능 제한

## 🔍 주요개념

### Docker 이미지 태깅 vs 일반 이미지

- **일반 Docker 이미지**: `my-model:latest` (로컬에서만 사용 가능)
- **ECR 태깅된 이미지**: `123456789012.dkr.ecr.us-east-1.amazonaws.com/my-model:latest` (AWS에서 인식 가능)

### 실전 적용

- **실시간 추론**: 웹 애플리케이션에서 밀리초 단위 응답이 필요한 경우
- **A/B 테스트**: 여러 모델 버전을 동시에 배포하여 성능 비교
- **자동 스케일링**: 트래픽 증가에 따른 인스턴스 자동 확장

## 📝 관련 문제

**Question:** A Workstation Learning Specialist previously trained a logistic regression model on a local machine using scikit-learn and now wishes to deploy it to production for the sole purpose of inference. What actions should be done to guarantee that an Amazon SageMaker model trained locally can be hosted?

**Options:**

- A) Build the Docker image with the inference code. Tag the Docker image with the registry hostname and upload it to Amazon ECR.
- B) Serialize the trained model so the format is compressed for deployment. Tag the Docker image with the registry hostname and upload it to Amazon S3.
- C) Serialize the trained model so the format is compressed for deployment. Build the image and upload it to Docker Hub.
- D) Build the Docker image with the inference code. Configure Docker Hub and upload the image to Amazon ECR.

**정답: A) Build the Docker image with the inference code. Tag the Docker image with the registry hostname and upload it to Amazon ECR.**

💡 추가 설명:

- **Option B** - S3는 모델 아티팩트 저장용이며, Docker 이미지 저장소가 아님
- **Option C** - Docker Hub는 SageMaker에서 지원하지 않는 외부 레지스트리
- **Option D** - Docker Hub 설정 후 ECR 업로드는 모순된 과정

---