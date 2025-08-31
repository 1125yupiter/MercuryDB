---

title: aws-ml-service-hierarchy-selection
created: 2025-08-21
modified: 2025-08-21
tags:
- service/aws-ml
- architecture/hierarchy
- decision/service-selection
- constraint/complexity-scale
- comparison/multi-service
aliases: ["aws-ml-hierarchy", "ml-service-selection", "aws-ml-stack"]

---

# AWS 머신러닝 서비스 계층구조와 선택 가이드

## 🎯 핵심 포인트

ML 워크로드의 복잡도와 규모에 따라 Lambda(초경량) → ECS/Fargate(중경량) → SageMaker(대형) → EMR(대규모) 순으로 적절한 AWS 서비스를 선택할 수 있다.

## 📝 설명

### AWS ML 서비스 계층구조의 설계 원리

AWS는 머신러닝 워크로드의 다양한 요구사항을 충족하기 위해 복잡도와 규모에 따른 계층적 서비스 구조를 제공합니다. 각 서비스는 특정 사용 사례에 최적화되어 있으며, 비용, 성능, 관리 복잡도의 트레이드오프를 고려하여 선택해야 합니다.

**계층구조의 핵심 기준:**
- 모델 크기와 복잡도
- 실행 시간 요구사항
- GPU 필요성
- 관리 기능 요구수준
- 비용 민감도

### 아키텍처 플로우

```
워크로드 분석
    ↓
복잡도 평가 (모델 크기, 실행시간, GPU 필요성)
    ↓
┌─────────────┬─────────────┬─────────────┬─────────────┐
│   Lambda    │ ECS/Fargate │ SageMaker   │    EMR      │
│  (초경량)    │   (중경량)   │   (대형)     │  (대규모)    │
└─────────────┴─────────────┴─────────────┴─────────────┘
    ↓             ↓             ↓             ↓
간단한 추론    컨테이너 기반   ML 특화 기능   빅데이터 처리
```

### Trade-offs 고려사항

**Lambda 장점**:
- 완전 서버리스, 관리 부담 없음
- 사용한 만큼만 과금 (매우 경제적)
- 자동 스케일링 (0→1000+ 인스턴스)
- 빠른 배포와 업데이트

**Lambda 단점**:
- 실행시간 제한 (15분)
- 메모리 제한 (10GB)
- GPU 지원 없음
- Cold Start 지연

**ECS/Fargate 장점**:
- 컨테이너 기반 유연성
- 기존 Docker 이미지 활용
- 비용 효율적 (Lambda보다 저렴한 장기 실행)
- 마이크로서비스 아키텍처 적합

**ECS/Fargate 단점**:
- GPU 지원 제한 (Fargate는 불가)
- 컨테이너 관리 복잡도
- 네트워킹 설정 필요

**SageMaker 장점**:
- ML 전용 최적화 인프라
- GPU 네이티브 지원
- A/B 테스트, 모니터링 내장
- 모델 레지스트리와 파이프라인

**SageMaker 단점**:
- 높은 비용 (전용 인스턴스)
- 학습 곡선 존재
- 간단한 작업에는 과도한 기능

**EMR 장점**:
- 대규모 분산 처리
- Spark MLlib 통합
- 비용 최적화 (Spot 인스턴스)
- 페타바이트급 데이터 처리

**EMR 단점**:
- 클러스터 관리 복잡도
- 높은 초기 설정 비용
- 실시간 추론 부적합

## 🔍 주요개념

### 서비스별 적용 영역

- **Lambda**: 실시간 API, 이벤트 기반 추론, 전처리 작업
- **ECS/Fargate**: 중간 규모 모델 서빙, 마이크로서비스 기반 ML
- **SageMaker**: 프로덕션 ML 모델 서빙, 훈련, MLOps
- **EMR**: 특성 엔지니어링, 대규모 배치 추론, 데이터 마이닝

### 비용 계층구조

```
시간당 비용 (동일한 워크로드 기준):
Lambda ($0.02) < Fargate ($0.05) < ECS ($0.08) < SageMaker ($0.12) < EMR ($0.15+)

단, 워크로드 특성에 따라 총 비용은 역전될 수 있음
```

### 실전 적용 시나리오

- **스타트업 MVP**: Lambda → 성장 시 ECS → 성숙기 SageMaker
- **이커머스 추천**: EMR(특성 추출) → SageMaker(훈련) → Lambda(실시간 추천)
- **이미지 분석 플랫폼**: Lambda(전처리) → ECS+GPU(객체 탐지) → SageMaker(배치 분석)

## 📝 관련 문제

**Question:** Your company is developing a machine learning application with the following requirements: real-time image classification for mobile apps (simple CNN model), batch processing of user behavior data (10TB daily), and training of recommendation models (complex deep learning). Which combination of AWS services would be most appropriate for this multi-tier ML architecture?

**Options:**

- A) Use SageMaker for all components to maintain consistency
- B) Lambda for image classification, EMR for batch processing, SageMaker for model training
- C) ECS for image classification, SageMaker for batch processing, Lambda for model training
- D) Fargate for all components with different container configurations
- E) EMR for all components using Spark MLlib

**정답: B) Lambda for image classification, EMR for batch processing, SageMaker for model training**

💡 추가 설명:

- **A) 모든 것을 SageMaker로** - 간단한 이미지 분류에는 과도하고 비용 비효율적
- **C) ECS + SageMaker + Lambda** - 모델 훈련에 Lambda는 시간 제한으로 부적합
- **D) 모든 것을 Fargate로** - 복잡한 딥러닝 훈련과 대규모 배치 처리에 부적합
- **E) 모든 것을 EMR로** - 실시간 이미지 분류에는 EMR이 과도하고 지연시간 문제