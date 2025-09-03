---

title: sagemaker-processing-vs-batch-transform
created: 2025-08-20
modified: 2025-08-20
tags:
- service/sagemaker
- method/preprocessing
- constraint/batch
- technique/naming-confusion
- usecase/image-preprocessing
aliases: ["processing-jobs", "batch-transform", "sagemaker-preprocessing"]

---

# SageMaker Processing Jobs vs Batch Transform - 네이밍 함정 구별하기

## 🎯 핵심 포인트

이미지 전처리 등 훈련 전 데이터 변환이 필요한 경우 SageMaker Processing Jobs에서 수행해야 하며, Batch Transform은 이름과 달리 훈련된 모델을 사용한 배치 추론 전용 서비스이다.

## 📝 설명

### AWS SageMaker의 교묘한 네이밍 함정

AWS SageMaker에서 가장 헷갈리는 부분 중 하나가 바로 **"Transform"이라는 용어의 이중적 사용**입니다. 이름만 보면 둘 다 데이터 변환 작업을 하는 것처럼 보이지만, 실제 용도는 완전히 다릅니다.

#### Batch Transform의 진실
- **이름**: "Batch Transform" (배치 변환)
- **실제 기능**: 훈련된 모델을 사용한 **배치 추론**
- **필수 요소**: 이미 훈련된 모델 필요
- **용도**: 대량 데이터에 대한 모델 예측

#### Processing Jobs의 실체
- **이름**: "Processing Jobs" (처리 작업)
- **실제 기능**: 데이터 **전처리 및 변환**
- **필수 요소**: 모델 불필요, 변환 로직만 필요
- **용도**: 훈련 전/후 데이터 가공

### 아키텍처 플로우

```
훈련 전 데이터 전처리:
S3 원본 이미지 → Processing Jobs → S3 전처리 데이터 → Training Jobs

배치 추론:
S3 입력 데이터 → Batch Transform (with 훈련된 모델) → S3 예측 결과
```

### Trade-offs 고려사항

**Processing Jobs 장점**:
- 모델 없이 순수 데이터 변환 가능
- 복잡한 전처리 로직 구현 가능
- scikit-learn, Spark 등 다양한 프레임워크 지원
- 커스텀 컨테이너 활용 가능

**Processing Jobs 단점**:
- 설정이 상대적으로 복잡
- 추론 파이프라인과의 직접 통합 제약

**Batch Transform 장점**:
- 훈련된 모델로 대량 추론 최적화
- 자동 스케일링 및 리소스 관리
- 실시간 엔드포인트 대비 비용 효율적

**Batch Transform 단점**:
- 반드시 훈련된 모델 필요
- 순수 데이터 변환 작업 불가능
- 이름으로 인한 용도 혼동

## 🔍 주요개념

### 핵심 개념 비교

- **Processing Jobs**: 모델 없이 데이터만 변환하는 전처리 전용 서비스
- **Batch Transform**: 훈련된 모델을 사용해 대량 데이터에 추론하는 배치 추론 서비스
- **Transform Jobs**: 문제에서 Processing Jobs를 지칭하는 용어 (혼동 주의)

### 실전 적용

- **E-commerce 이미지 분류**: 제품 이미지 리사이징, 그레이스케일 변환 → Processing Jobs
- **의료 영상 전처리**: DICOM 이미지 표준화 및 크기 조정 → Processing Jobs  
- **대량 제품 추천**: 훈련된 모델로 수천만 사용자 추천 생성 → Batch Transform

## 📝 관련 문제

**Question:** You are building a deep learning model to classify images in an ecommerce platform. You want to train your model using Amazon SageMaker with a large dataset of images stored in S3. You need to preprocess the images before training by resizing them to a specific size and converting them to grayscale. Which approach should you use?

**Options:**

- A) Use Amazon SageMaker's built-in batch transform to resize and convert the images to grayscale before training
- B) Use Amazon SageMaker's built-in transform jobs to resize and convert the images to grayscale before training  
- C) Use Amazon SageMaker's built-in hyperparameter tuning to preprocess the images before training
- D) Use Amazon SageMaker's built-in training jobs to resize and convert the images to grayscale during training

**정답: B) Use Amazon SageMaker's built-in transform jobs**

💡 추가 설명:

- **A) Batch Transform** - 이름과 달리 훈련된 모델을 사용한 배치 추론 전용이므로 훈련 전 전처리에 부적절
- **C) Hyperparameter Tuning** - 모델의 최적 하이퍼파라미터 탐색용으로 데이터 전처리와 무관
- **D) Training Jobs** - 모델 훈련에 집중해야 하므로 별도 전처리 단계가 더 효율적