---

title: classification-rekognition-custom-labels-ecommerce
created: 2025-08-23
modified: 2025-08-23
tags:
- service/rekognition
- problem/classification
- usecase/ecommerce
- constraint/accuracy
- technique/data-augmentation
aliases: ["rekognition-custom", "custom-labels", "product-classification"]

---

# 이커머스 제품 분류를 위한 Amazon Rekognition Custom Labels 적용

## 🎯 핵심 포인트

이커머스에서 제품 사진을 구체적인 카테고리로 분류해야 하는 경우 Amazon Rekognition Custom Labels를 사용하여 비즈니스 특화된 분류 모델을 구축할 수 있다.

## 📝 설명

### Amazon Rekognition Custom Labels가 이커머스 제품 분류에 적합한 이유

Amazon Rekognition Custom Labels는 사용자 정의 이미지 분류 모델을 구축할 수 있는 관리형 서비스입니다. 기존의 DetectLabels API와 달리 비즈니스 요구사항에 맞는 특화된 라벨을 정의하고 학습할 수 있어, 이커머스의 복잡한 제품 분류 요구사항에 적합합니다.

**핵심 특징:**
- 사용자 정의 라벨로 학습 데이터 구성
- 브랜드별, 스타일별 세부 분류 가능
- 빠른 모델 구축과 배포
- 자동 모델 튜닝 및 최적화

### 아키텍처 플로우

```
제품 이미지 (S3) → Custom Labels 학습 데이터셋 구성
                ↓
              모델 학습 및 평가
                ↓
              새로운 제품 이미지 → Custom Labels 추론 → 구체적 제품 분류
                                                      (예: "나이키 러닝화 - 남성용")
```

### 모델 성능 향상 전략

**데이터 증강 (Data Augmentation)**:
- 이미지 회전, 뒤집기, 크기 조정
- 밝기, 대비, 색상 조정
- 학습 데이터 다양성 확보

**클래스 불균형 해결**:
- 제품 카테고리별 데이터 분포 분석
- 오버샘플링/언더샘플링 적용
- 균형잡힌 학습 데이터셋 구성

### Trade-offs 고려사항

**Custom Labels 장점**:
- 비즈니스 특화된 정확한 분류
- 빠른 모델 구축 및 배포
- 자동 모델 최적화
- 관리형 서비스로 운영 부담 최소화

**Custom Labels 단점**:
- 충분한 학습 데이터 필요
- 카테고리 변경 시 재학습 필요
- DetectLabels 대비 비용 상승

**DetectLabels 장점**:
- 사전 훈련된 모델로 즉시 사용 가능
- 일반적인 객체 인식에 효과적
- 상대적으로 저렴한 비용

**DetectLabels 단점**:
- 일반적인 분류만 가능 ("신발", "의류" 수준)
- 세부 제품 분류 불가능
- 브랜드나 스타일 구분 어려움

## 🔍 주요개념

### 서비스별 분류 능력 비교

- **DetectLabels**: 일반적 객체 인식 - "신발", "가방", "의류" 등 범용 카테고리 수준
- **Custom Labels**: 비즈니스 특화 분류 - "나이키 에어맥스", "아디다스 스탠스미스", "구찌 핸드백" 등 구체적 제품 분류

### 실전 적용

- **패션 이커머스**: 브랜드별, 스타일별, 시즌별 의류 분류
- **전자제품**: 제조사별, 모델별, 사양별 디바이스 분류  
- **생활용품**: 용도별, 브랜드별, 크기별 상품 분류

## 📝 관련 문제

**Question:** An ecommerce startup is automating product categorization using photographs. A data scientist used Amazon SageMaker image categorization to train a computer vision model. When classifying new items, the model's accuracy is insufficient. The company wants to enhance the model quickly. Which actions would increase the solution's accuracy? (Select three.)

**Options:**

- A) Use the SageMaker semantic segmentation algorithm to train a new model
- B) Use the Amazon Rekognition DetectLabels API to classify products
- C) Augment images in the dataset using crop, resize, flip, rotate operations
- D) Implement pixel normalization and image scaling using SageMaker notebook
- E) Use Amazon Rekognition Custom Labels to train a new model
- F) Check for class imbalances and apply oversampling/undersampling

**정답: C, E, F) Data Augmentation + Custom Labels + Class Balancing**

💡 추가 설명:

- **Option A (Semantic Segmentation)** - 픽셀 단위 영역 분할 기법으로 제품 분류 문제에 부적합
- **Option B (DetectLabels API)** - 일반적인 객체만 인식 가능하여 세부 제품 분류 불가능
- **Option D (Pixel Normalization)** - 이미 동일한 크기로 전처리된 상황에서 추가 효과 제한적

---