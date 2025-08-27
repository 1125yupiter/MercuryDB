---

title: rekognition-custom-convention-realtime
created: 2025-08-19
modified: 2025-08-19
tags:
- service/rekognition
- usecase/brand-detection
- constraint/real-time
- method/computer-vision
- industry/events
aliases: ["custom-labels", "shirt-detection", "convention-cam"]

---

# Amazon Rekognition Custom Labels를 활용한 컨벤션 브랜드 셔츠 자동 감지 시스템

## 🎯 핵심 포인트

컨벤션에서 특정 브랜드 셔츠를 착용한 참가자를 자동으로 감지해야 하는 경우, Amazon Rekognition Custom Labels에서 브랜드 셔츠 이미지로 커스텀 모델을 훈련시키고 실시간 카메라 피드에서 추론을 실행할 수 있다.

## 📝 설명

### Amazon Rekognition Custom Labels가 브랜드 의류 감지에 적합한 이유

Amazon Rekognition Custom Labels는 사용자 정의 객체, 로고, 브랜드 요소를 감지하기 위해 특별히 설계된 컴퓨터 비전 서비스입니다. 기존의 일반적인 객체 감지와 달리, 특정 브랜드나 로고처럼 고유한 시각적 요소를 학습할 수 있어 컨벤션과 같은 이벤트에서 브랜드 프로모션 목적의 의류 감지에 최적화되어 있습니다.

이 서비스는 최소 10개의 라벨링된 이미지만으로도 모델을 훈련할 수 있으며, AutoML 기술을 활용해 복잡한 머신러닝 지식 없이도 고품질의 커스텀 감지 모델을 구축할 수 있습니다. 또한 실시간 추론을 지원하여 카메라 피드에서 즉시 감지 결과를 얻을 수 있습니다.

### 아키텍처 플로우

```
카메라 피드 → 프레임 추출 → Rekognition Custom Labels → 감지 결과 → 이벤트 트리거
     ↓               ↓                    ↓               ↓              ↓
실시간 스트림 → Lambda 함수 → 훈련된 모델 → 신뢰도 점수 → 프로모션 액션
                    ↓
              S3 이미지 저장
```

### Trade-offs 고려사항

**Amazon Rekognition Custom Labels 장점**:
- 브랜드별 커스텀 객체 감지에 특화된 설계
- 최소한의 훈련 데이터로도 높은 정확도 달성 가능
- 실시간 추론 지원으로 즉시 감지 결과 제공
- 로우코드/노코드 접근 방식으로 빠른 구현
- 다양한 각도, 조명, 거리에서도 안정적인 감지 성능

**Amazon Rekognition Custom Labels 단점**:
- 모델 훈련 및 호스팅 비용 발생 (사용하지 않을 때도 과금)
- 새로운 브랜드나 디자인 변경 시 재훈련 필요
- 복잡한 배경이나 가려진 로고의 경우 정확도 저하 가능성

**Amazon SageMaker Ground Truth 장점**:
- 대규모 데이터 라벨링에 최적화
- 다양한 라벨링 워크플로우 지원

**Amazon SageMaker Ground Truth 단점**:
- 데이터 준비 도구로 실시간 감지 불가능
- 별도 추론 인프라 구축 필요

**Amazon Lookout for Vision 장점**:
- 산업용 이상 감지에 특화된 높은 정확도

**Amazon Lookout for Vision 단점**:
- 제조업 결함 감지 용도로 설계되어 의류 브랜딩에 부적합
- 사람의 다양한 포즈와 움직임에 대한 학습 한계

**Amazon Transcribe 장점**:
- 음성-텍스트 변환의 높은 정확도

**Amazon Transcribe 단점**:
- 시각적 요소 감지 불가능
- 의류나 로고 인식과 전혀 무관한 서비스

## 🔍 주요개념

### 컴퓨터 비전 감지 방식 비교

- **Object Detection**: 이미지 내 여러 객체의 위치와 클래스를 동시에 식별하는 기술로, 브랜드 로고의 정확한 위치 파악 가능
- **Image Classification**: 전체 이미지를 하나의 클래스로 분류하는 기술로, 셔츠 착용 여부의 전반적 판단에 활용
- **Custom Label Training**: 특정 브랜드나 로고처럼 범용 모델에서 다루지 않는 고유한 객체를 학습하는 전문 훈련 방식

### 실전 적용

- **패션 브랜드 컨벤션**: 신제품 런칭 이벤트에서 브랜드 티셔츠 착용 고객 자동 식별 및 리워드 제공
- **기업 컨퍼런스**: 회사 로고가 있는 의류 착용 직원들의 출입 통계 및 참여도 분석
- **스포츠 이벤트**: 특정 팀 유니폼이나 굿즈 착용 팬들을 대상으로 한 실시간 프로모션 이벤트 진행

## 📝 관련 문제

**Question:** A convention plans to implement a system where cameras automatically detect when conference attendees are spotted wearing a specific company's shirt as part of a promotional contest. Which of the following approaches would be viable for implementing this feature?

**Options:**

- A) Use Amazon Rekognition Custom Labels to train a model with images of the branded shirts, then run inference on camera feeds
- B) Use Amazon SageMaker Ground Truth to continuously label live video data and trigger events in real time
- C) Use Amazon Transcribe to extract audio cues from video feeds and infer shirt detection based on keyword frequency
- D) Use Amazon Lookout for Vision to detect shirt logos from streaming video based on anomaly detection

**정답: A) Amazon Rekognition Custom Labels to train a model with images of the branded shirts, then run inference on camera feeds**

💡 추가 설명:

- **Option B (SageMaker Ground Truth)** - 데이터 라벨링 도구로 실시간 추론 기능이 없어 카메라 피드에서 즉시 감지 불가능
- **Option C (Amazon Transcribe)** - 음성 처리 서비스로 시각적 객체나 의류 감지와 전혀 무관함
- **Option D (Lookout for Vision)** - 제조업 품질 관리용 이상 감지 서비스로 다양한 인간 포즈와 의류 감지에 부적합