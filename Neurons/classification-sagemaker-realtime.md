---
title: classification-sagemaker-realtime.md
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/classification
- service/sagemaker
- constraint/real-time
- method/deep-learning
- technique/data-augmentation
aliases: ["image-classification", "sagemaker-image", "product-discovery"]
---

# Product Image Classification with SageMaker – Improving Accuracy via Data Augmentation

## 🎯 핵심 포인트
실제 배포 환경에서 검증 정확도가 하락한 경우, SageMaker 이미지 분류 모델에서 데이터 증강 기법을 활용하면 정확도를 개선할 수 있다.

## 📝 설명  

### SageMaker가 제품 이미지 분류에 적합한 이유
Amazon SageMaker Image Classification 알고리즘은 ResNet 기반의 CNN을 사용하여 이미지 분류 문제를 해결한다.  
- 고객 휴대폰으로 촬영한 이미지 → 다양한 조명, 각도, 배경이 존재하여 학습/검증 데이터와 분포가 다를 수 있음.  
- 이로 인해 프로덕션 배포 후 정확도가 감소하는 현상이 발생.  
- 데이터 증강 기법을 통해 원본 학습 데이터를 다양한 시나리오로 변형함으로써 모델의 일반화 성능을 강화할 수 있음.  

SageMaker에서는 `augmentation_type` 하이퍼파라미터를 통해 자동으로 **랜덤 크롭, 색상 변환, 수평 뒤집기** 등을 적용할 수 있다.  

### 아키텍처 플로우
[고객 휴대폰 촬영 이미지]
↓
[S3 업로드]
↓
[SageMaker Image Classification (ResNet 기반 CNN)]
↓
[데이터 증강 적용 → 학습/검증]
↓
[모델 배포 (SageMaker Endpoint)]
↓
[실시간 제품 이미지 분류 → 고객 앱 노출]

### Trade-offs 고려사항  

**데이터 증강 장점**:
- 학습 데이터 다양성 확보 → 실제 환경에 대한 일반화 성능 향상
- 추가적인 데이터 수집 비용 절감
- 자동 하이퍼파라미터 지원으로 적용 용이  

**데이터 증강 단점**:
- 학습 시간 증가
- 일부 증강 기법이 과도하면 원본 특성을 손상시킬 수 있음  

**대안 (다른 신경망 모델 활용) 장점**:
- 아키텍처 변경으로 새로운 성능 향상 가능성 존재  

**대안 (다른 신경망 모델 활용) 단점**:
- 높은 비용과 시간 소모
- 일반화 문제를 근본적으로 해결하지 못할 수 있음  

## 🔍 주요개념  

### 데이터 증강 vs 정규화  

- **데이터 증강 (Data Augmentation)**: 원본 이미지를 회전, 색상 변경, 크롭 등 변형하여 학습 데이터를 늘리고 일반화 성능 강화.  
- **정규화 (Normalization/Scaling)**: RGB 값을 0~1 범위로 조정하여 학습 안정성과 수렴 속도 개선. 정확도 향상 효과는 제한적.  

### 실전 적용  

- 📸 **리테일 앱**: 고객이 올린 제품 사진을 자동 분류 → 데이터 증강으로 다양한 조명 조건 대응  
- 🏪 **마트 상품 검색**: 스캔된 상품 이미지 → 학습 데이터 다양화로 인식률 향상  
- 📦 **전자상거래 플랫폼**: 사용자 업로드 이미지 기반 추천 시스템 → 데이터 증강 적용 시 추천 품질 개선  

## 📝 관련 문제  

**Question:** To improve product discoverability, a company has decided to train a model that will classify products from images taken from their customer’s mobile phones. The company has reached a 95.62% validation accuracy. However, the accuracy dropped when the model was deployed in production. Which approach should be used to improve the model accuracy?  

**Options:**  
- A) Generate more training data using data augmentation techniques  
- B) Use scaling on the image data to bring all RGB values within the range of 0-1  
- C) Perform t-SNE on image data to reduce highly correlated features  
- D) Train a different type of neural network model using the existing image data  

**정답: A) Generate more training data using data augmentation techniques**  

💡 추가 설명:  
- **(B)** 정규화는 학습 속도 개선에 도움은 되지만 일반화 문제를 해결하지 못함  
- **(C)** t-SNE는 주로 데이터 시각화 목적, 이미지 학습용 차원 축소에는 적합하지 않음  
- **(D)** 아키텍처 변경은 비용과 시간이 크며 정확도 개선을 보장하지 않음  
