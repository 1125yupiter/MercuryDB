---

title: semantic-segmentation-sagemaker-selfdriving
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/semantic-segmentation
- service/sagemaker
- usecase/autonomous-vehicle
- technique/deep-learning
- algorithm/resnet50
aliases: ["semantic-seg", "pixel-classification", "self-driving-cv"]

---

# Semantic Segmentation을 활용한 자율주행차 컴퓨터 비전 시스템

## 🎯 핵심 포인트

자율주행차에서 카메라 이미지의 모든 픽셀을 분류해야 하는 경우 SageMaker built-in semantic segmentation 알고리즘에서 pre-trained ResNet50 백본을 사용하여 픽셀 단위 분류를 수행할 수 있다.

## 📝 설명

### SageMaker Semantic Segmentation이 자율주행차 비전에 적합한 이유

SageMaker의 semantic segmentation 알고리즘은 이미지의 모든 픽셀을 특정 클래스로 분류하는 dense prediction 작업에 특화되어 있습니다. 자율주행차는 도로 환경에서 실시간으로 정밀한 환경 인식이 필요하며, 이를 위해서는 단순한 객체 탐지를 넘어 픽셀 단위의 정확한 분할이 필수적입니다.

SageMaker는 세 가지 주요 알고리즘을 제공합니다:
- **FCN (Fully-Convolutional Network)**: 기본적인 semantic segmentation 구조
- **PSP (Pyramid Scene Parsing)**: 다중 스케일 컨텍스트 정보 활용
- **DeepLabV3**: Atrous convolution으로 고해상도 특징 추출

Pre-trained ResNet50 백본을 사용하면 ImageNet에서 학습된 풍부한 시각적 특징을 전이학습으로 활용할 수 있어, 자율주행 도메인 데이터셋으로 효율적인 fine-tuning이 가능합니다.

### 아키텍처 플로우

```
카메라 센서 → 이미지 캡처 → 전처리 → ResNet50 백본
                                            ↓
픽셀별 클래스 출력 ← Semantic Segmentation Head ← Feature Maps
    ↓
클래스: [도로, 차량, 보행자, 건물, 표지판, 하늘, 나무 등]
    ↓
자율주행 의사결정 시스템
```

### Trade-offs 고려사항

**SageMaker Semantic Segmentation 장점**:
- 픽셀 단위의 정밀한 분류 제공
- 전이학습으로 효율적인 모델 개발
- 관리형 서비스로 인프라 부담 최소화
- 다양한 알고리즘 선택지 제공 (FCN, PSP, DeepLabV3)

**SageMaker Semantic Segmentation 단점**:
- 실시간 추론을 위한 최적화 필요
- 고해상도 이미지 처리 시 연산 비용 증가
- 엣지 디바이스 배포 시 추가 최적화 작업 필요

**Object Detection (Faster R-CNN) 장점**:
- 빠른 추론 속도
- 객체별 정확한 위치 정보 제공

**Object Detection (Faster R-CNN) 단점**:
- 픽셀 단위 분류 불가능
- Bounding box 형태의 제한적 위치 정보
- 복잡한 도로 환경에서 정밀도 부족

**Amazon Rekognition 장점**:
- 사전 훈련된 모델로 즉시 사용 가능
- 다양한 객체 인식 기능

**Amazon Rekognition 단점**:
- Semantic segmentation 미지원
- 자율주행 특화 클래스 부족
- 픽셀 단위 분류 불가능

## 🔍 주요개념

### 핵심 개념 비교

- **Semantic Segmentation**: 이미지의 모든 픽셀을 의미적 클래스로 분류하는 기법. 같은 클래스의 객체들을 구분하지 않고 하나의 영역으로 처리
- **Instance Segmentation**: 같은 클래스 내에서도 개별 객체를 구분하여 분할하는 기법. 예를 들어 여러 대의 차량을 각각 다른 인스턴스로 구분
- **Object Detection**: 객체 주변에 사각형 경계 상자를 그려 위치와 클래스를 예측하는 기법. 정확한 형태는 알 수 없음

### 실전 적용

- **차선 인식 시스템**: 도로 표면의 차선을 픽셀 단위로 정확히 분할하여 차량의 주행 경로 결정
- **보행자 안전 시스템**: 보행자의 정확한 실루엣을 파악하여 충돌 위험 예측 및 긴급 제동 시스템 작동
- **주차 보조 시스템**: 주차 공간의 경계를 픽셀 단위로 인식하여 자동 주차 경로 계획

## 📝 관련 문제

**Question:** As a data scientist involved in the development of a self-driving car system, your task is to implement a computer vision solution capable of categorizing every pixel in images captured by the car's cameras. The categories include identifying objects like people, buildings, roads, signs, and vehicles. How would you implement a computer vision solution capable of classifying every pixel in images captured by the car's cameras?

**Options:**

- A) Customize Amazon Rekognition Custom Labels to build a pixel-level image classification model for analyzing the car's camera images
- B) Fine-tune the SageMaker built-in semantic segmentation algorithm using a pre-trained ResNet50 backbone
- C) Customize Amazon Rekognition to process the car's camera video streams
- D) Fine-tune the SageMaker built-in object detection algorithm using a pre-trained Faster R-CNN backbone

**정답: B) Fine-tune the SageMaker built-in semantic segmentation algorithm using a pre-trained ResNet50 backbone**

💡 추가 설명:

- **Amazon Rekognition Custom Labels** - 객체 탐지와 이미지 분류에 특화되어 있어 픽셀 단위 분류(semantic segmentation) 작업에는 부적합
- **Amazon Rekognition Video Processing** - 비디오 스트림 처리에 초점을 맞춘 서비스로 픽셀 단위의 정밀한 분류 작업에는 최적화되지 않음
- **Object Detection (Faster R-CNN)** - 객체 주변에 bounding box만 제공하므로 픽셀 단위의 정밀한 분류는 불가능