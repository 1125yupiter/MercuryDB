---
title: metro-safety-sagemaker-segmentation
created: 2025-08-22
modified: 2025-08-22
tags:
- service/sagemaker-groundtruth
- technique/semantic-segmentation
- constraint/privacy-sensitive
- usecase/safety-monitoring
- deployment/computer-vision
aliases: ["metro-safety", "yellow-line-detection", "safety-segmentation"]

---

# 지하철 안전선 모니터링을 위한 SageMaker Ground Truth Semantic Segmentation

## 🎯 핵심 포인트

지하철 역에서 노란 안전선 위반을 실시간 탐지해야 하는 경우, 기존 객체 탐지(object detection)가 효과적이지 않을 때 Amazon SageMaker Ground Truth의 semantic segmentation 라벨링 작업과 private workforce를 사용하여 픽셀 단위의 정밀한 영역 분할로 안전선과 승객의 위치 관계를 정확히 파악할 수 있다.

## 📝 설명

### SageMaker Ground Truth Semantic Segmentation이 안전선 모니터링에 적합한 이유

**핵심 문제**: 기존 object detection 모델이 노란 안전선, 사람, 기차를 효과적으로 구분하지 못하는 상황

**Semantic Segmentation의 장점**:
- **픽셀 단위 정밀도**: 노란선과 같은 불규칙하고 연속적인 영역을 정확히 분할
- **공간적 관계 파악**: 사람이 안전선을 넘었는지 정확한 위치 관계 판단 가능
- **복잡한 형태 처리**: 직선이 아닌 곡선이나 분기되는 안전선도 효과적으로 라벨링

**Private Workforce의 필요성**:
- 지하철 역 CCTV는 민감한 개인정보 포함
- 데이터 보안 요구사항 충족
- 일관성 있는 라벨링 품질 보장

### 아키텍처 플로우

```
비디오 피드 수집
    ↓
프레임 추출 및 전처리
    ↓
SageMaker Ground Truth
    ↓
Private Workforce Labeling
(Semantic Segmentation)
    ↓
라벨링된 데이터셋
    ↓
Custom 딥러닝 모델 훈련
    ↓
실시간 안전선 위반 탐지
    ↓
역무실 알림 시스템
```

### Trade-offs 고려사항

**SageMaker Ground Truth Semantic Segmentation 장점**:
- 픽셀 단위의 정밀한 영역 분할
- 복잡한 형태의 객체(노란선) 정확한 라벨링
- Private workforce로 데이터 보안 보장
- AWS 생태계 통합으로 원활한 MLOps

**SageMaker Ground Truth Semantic Segmentation 단점**:
- Object detection 대비 라벨링 시간과 비용 증가
- 더 복잡한 모델 아키텍처 필요
- 실시간 추론 시 더 높은 컴퓨팅 리소스 요구

**Amazon Rekognition Custom Labels 장점**:
- 빠른 모델 개발 및 배포
- AWS 관리형 서비스로 운영 부담 적음
- A2I 통합으로 저신뢰도 예측 재검토 가능

**Amazon Rekognition Custom Labels 단점**:
- 여전히 bounding box 기반 접근법
- 노란선과 같은 선형 객체에 부적합
- 공간적 위치 관계 파악의 정밀도 한계

## 🔍 주요개념

### 라벨링 방식 비교

- **Object Detection**: 객체 주변에 사각형 경계 상자 생성, 전체적인 객체 위치만 파악
- **Semantic Segmentation**: 각 픽셀을 특정 클래스로 분류, 정확한 영역 경계 및 형태 파악

### 실전 적용

- **승강장 안전 관리**: 승객이 노란선을 넘었을 때 실시간 경고 알림
- **혼잡도 분석**: 안전선 근처 승객 밀도 측정으로 혼잡 시간대 파악
- **사고 예방**: 기차 진입 시 안전선 위반자 자동 탐지 및 안내방송

## 📝 관련 문제

**Question:** A metro station wants to develop a deep learning system to detect when passengers cross the yellow safety line when no train is present. The company has video feeds and needs to ensure data privacy. Initial object detection models cannot effectively distinguish between the yellow line, people crossing it, and trains. Which labeling strategy will help optimize this model?

**Options:**

- A) Use Amazon Rekognition Custom Labels with private workforce and Amazon A2I for low-confidence predictions
- B) Use Amazon SageMaker Ground Truth object detection labeling with Amazon Mechanical Turk workforce
- C) Use Amazon Rekognition Custom Labels with third-party AWS Marketplace vendor workforce and Amazon A2I
- D) Use Amazon SageMaker Ground Truth semantic segmentation labeling with private workforce
- E) Use Amazon Textract for document analysis with custom labeling workflow

**정답: D) Amazon SageMaker Ground Truth semantic segmentation labeling with private workforce**

💡 추가 설명:

- **Option A** - Rekognition Custom Labels는 여전히 bounding box 기반이라 선형 객체인 노란선 처리에 부적합
- **Option B** - Mechanical Turk는 공개 workforce라 민감한 비디오 데이터 보안 요구사항 위반
- **Option C** - 마찬가지로 bounding box 한계가 있고, 제3자 업체 사용으로 보안 리스크 존재
- **Option E** - Textract는 문서 분석 서비스로 비디오 분석과 무관