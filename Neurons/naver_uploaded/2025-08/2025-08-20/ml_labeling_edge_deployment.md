---

title: labeling-sagemaker-groundtruth-edge
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/object-detection
- service/sagemaker-groundtruth
- service/iot-greengrass
- constraint/budget-limited
- deployment/edge
aliases: ["ground-truth", "edge-labeling", "hybrid-labeling"]

---

# ML 이미지 라벨링과 엣지 배포 - SageMaker Ground Truth & IoT Greengrass

## 🎯 핵심 포인트

예산과 인력이 제한된 환경에서 대량의 이미지 라벨링이 필요한 경우, 기존 훈련된 모델과 AWS IoT Greengrass V2를 활용하여 엣지에서 하이브리드 라벨링을 수행할 수 있다.

## 📝 설명

### SageMaker Ground Truth가 하이브리드 라벨링에 적합한 이유

Amazon SageMaker Ground Truth는 자동화된 데이터 라벨링과 인간 라벨링을 결합한 하이브리드 접근 방식을 제공합니다. 기존 훈련된 모델을 활용하여 고신뢰도 예측은 자동으로 처리하고, 저신뢰도 이미지만 인간 라벨러에게 전달함으로써 전체 라벨링 비용과 시간을 크게 절약할 수 있습니다.

AWS IoT Greengrass V2는 엣지 환경에서 머신러닝 추론을 가능하게 하여, 제한적인 인터넷 연결 환경에서도 로컬에서 이미지를 분류하고 필터링할 수 있습니다. 이를 통해 대역폭 소비를 줄이고 처리 비용을 최적화할 수 있습니다.

### 아키텍처 플로우

```
[원격 산업 환경] 
    ↓ (20,000 unlabeled images)
[IoT Greengrass V2 + 기존 훈련된 모델]
    ↓ (로컬 추론 수행)
[신뢰도 점수 기반 필터링]
    ├── 고신뢰도 → 자동 라벨링 완료
    └── 저신뢰도 → Amazon S3 업로드
            ↓
[SageMaker Ground Truth]
    ↓ (5명 인간 라벨러 팀)
[최종 고품질 라벨링된 데이터셋]
```

### Trade-offs 고려사항

**SageMaker Ground Truth + IoT Greengrass 장점**:
- 기존 모델 활용으로 라벨링 비용 대폭 절감
- 엣지에서 로컬 처리로 대역폭 사용량 최소화
- 하이브리드 접근으로 데이터 품질 보장
- 제한적 인터넷 환경에서도 운영 가능

**SageMaker Ground Truth + IoT Greengrass 단점**:
- 초기 엣지 디바이스 설정 복잡성
- 기존 모델의 도메인 적합성에 따른 성능 차이
- 엣지 하드웨어 리소스 요구사항

**Amazon IQ 장점**:
- 전문가 네트워크 접근 가능

**Amazon IQ 단점**:
- 추가 비용 발생으로 예산 제약에 부적합
- 단순 프리랜싱 플랫폼으로 라벨링 기능 없음

## 🔍 주요개념

### 핵심 개념 비교

- **SageMaker Ground Truth**: AWS의 데이터 라벨링 서비스로 자동화와 인간 라벨링을 결합하여 고품질 훈련 데이터 생성
- **IoT Greengrass V2**: 엣지 디바이스에서 AWS 서비스를 로컬로 실행할 수 있게 하는 엣지 컴퓨팅 플랫폼
- **SageMaker Neo**: 모델 최적화 서비스로 라벨링과는 무관한 추론 성능 개선 도구

### 실전 적용

- 제조업 품질 검사: 공장 현장에서 결함 이미지 실시간 필터링 후 애매한 케이스만 전문가 검토
- 의료 영상 분석: 병원 내 엣지에서 1차 스크리닝 후 의심 케이스만 방사선의 판독 의뢰
- 자율주행 데이터: 차량에서 수집된 영상 중 특이 상황만 선별하여 라벨링 작업 효율화

## 📝 관련 문제

**Question:** A Machine Learning Specialist has received 20,000 unlabeled images to train an object detection model. The Specialist currently has a team of 5 human labelers. Due to resource and budget constraints, the Specialist chooses to leverage a trained labeling model from a previous job to annotate the images. The Specialist also wants to deploy the trained model at the edge to process images in remote industrial environments with limited internet connectivity. Which is the BEST course of action to label the images while meeting these requirements?

**Options:**

- A) Label the dataset using the trained model and send those images with a low-confidence score to Amazon S3. Then, have Amazon SageMaker Neo label them.
- B) Outsource the labeling task from Amazon IQ. Use the trained model to verify labeled images with high-confidence scores. Finally, use only high-quality data to train the object detection model.
- C) Deploy the trained model using AWS IoT Greengrass V2 to perform inference at the edge. Send images with a low-confidence score to Amazon S3, and use Amazon SageMaker Ground Truth for human labeling.
- D) Outsource the labeling task from a private workforce of 50 labelers. Use the trained model to filter labeled images with low-confidence scores. Finally, use the filtered dataset to train the object detection model.

**정답: C) Deploy the trained model using AWS IoT Greengrass V2 to perform inference at the edge. Send images with a low-confidence score to Amazon S3, and use Amazon SageMaker Ground Truth for human labeling.**

💡 추가 설명:

- **Option A** - SageMaker Neo는 모델 최적화 서비스이지 라벨링 서비스가 아님
- **Option B** - Amazon IQ는 프리랜싱 플랫폼으로 추가 비용 발생하며 라벨링 기능 없음
- **Option D** - 50명 라벨러 고용은 예산 제약에 반하며 비효율적 리소스 사용