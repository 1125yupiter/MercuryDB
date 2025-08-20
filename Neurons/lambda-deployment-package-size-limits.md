---

title: lambda-deployment-package-size-limits
created: 2025-08-20
modified: 2025-08-20
tags:
- service/lambda
- constraint/size-limits
- deployment/package
- architecture/serverless
- optimization/bundle-size
aliases: ["lambda-limits", "deployment-size", "package-limits"]

---

# AWS Lambda 배포 패키지 크기 제한과 최적화 전략

## 🎯 핵심 포인트

Lambda 함수를 배포하는 경우 배포 방법에 따라 직접 업로드 시 50MB, S3 업로드 시 250MB, 컨테이너 이미지 사용 시 10GB까지 배포할 수 있다.

## 📝 설명

### AWS Lambda가 서버리스 컴퓨팅에 적합한 이유

AWS Lambda는 서버 관리 없이 코드를 실행할 수 있는 서버리스 컴퓨팅 서비스입니다. 하지만 함수 코드와 종속성을 포함한 배포 패키지의 크기에는 제한이 있어, 효율적인 패키지 관리가 필요합니다. 특히 머신러닝 모델이나 대용량 라이브러리를 사용하는 경우 이러한 제한사항을 이해하고 적절한 해결책을 선택해야 합니다.

### 아키텍처 플로우

```
개발자 코드 → 패키지 빌드 → 배포 방법 선택
                               ↓
직접 업로드 (50MB) ← → S3 업로드 (250MB) ← → 컨테이너 이미지 (10GB)
       ↓                    ↓                         ↓
   Lambda 함수         Lambda 함수                Lambda 함수
   (빠른 배포)        (중간 크기)              (대용량 처리)
       ↓                    ↓                         ↓
   실행 환경           실행 환경                 실행 환경
```

### Trade-offs 고려사항

**직접 업로드 장점**:
- 빠른 배포 프로세스
- 간단한 워크플로우
- 추가 인프라 불필요

**직접 업로드 단점**:
- 50MB 크기 제한
- 대용량 라이브러리 사용 제약
- 복잡한 ML 모델 배포 어려움

**S3 업로드 장점**:
- 250MB까지 지원
- 버전 관리 용이
- CI/CD 파이프라인 통합 가능

**S3 업로드 단점**:
- 추가 S3 설정 필요
- 배포 프로세스 복잡화
- 여전히 대용량 모델에는 제약

**컨테이너 이미지 장점**:
- 10GB까지 지원
- 표준 Docker 워크플로우
- 복잡한 런타임 환경 지원

**컨테이너 이미지 단점**:
- 콜드 스타트 시간 증가
- ECR 비용 추가
- 복잡한 설정 과정

## 🔍 주요개념

### 배포 방법별 크기 제한

- **직접 업로드**: 압축된 ZIP 파일 기준 50MB 제한
- **S3 업로드**: 압축 해제된 상태 기준 250MB 제한
- **Lambda Layers**: 함수와 모든 레이어 합계 250MB 제한
- **컨테이너 이미지**: ECR을 통한 최대 10GB 지원

### 실전 적용

- **웹 API 백엔드**: 일반적으로 직접 업로드로 충분 (Node.js, Python 기본 프레임워크)
- **데이터 처리 함수**: S3 업로드로 pandas, NumPy 등 대용량 라이브러리 포함
- **ML 추론 서비스**: 컨테이너 이미지로 TensorFlow, PyTorch 모델 배포

## 📝 관련 문제

**Question:** A company needs to deploy a machine learning model that requires TensorFlow and multiple Python libraries totaling 400MB in size. The model needs to process requests with sub-second latency. What is the most appropriate deployment approach for AWS Lambda?

**Options:**

- A) Use direct upload with ZIP package optimization
- B) Split the function into multiple smaller Lambda functions
- C) Use Lambda Layers to distribute the dependencies
- D) Deploy using Lambda container images
- E) Use S3 upload with dependency optimization

**정답: D) Deploy using Lambda container images**

💡 추가 설명:

- **Option A** - 직접 업로드는 50MB 제한으로 400MB 패키지 배포 불가능
- **Option B** - 함수 분할로는 개별 라이브러리 크기 문제 해결 안됨
- **Option C** - Lambda Layers도 총 250MB 제한으로 400MB 패키지 처리 불가능
- **Option E** - S3 업로드도 250MB 제한으로 400MB 패키지 배포 불가능

---