---

title: nlp-comprehend-sagemaker-custom
created: 2025-08-26
modified: 2025-08-26
tags:
- service/comprehend
- service/sagemaker
- problem/classification
- constraint/fastest-development
- technique/custom-algorithm
aliases: ["comprehend-custom-nlp", "sagemaker-mxnet", "nlp-preprocessing"]

---

# NLP에서 Amazon Comprehend와 SageMaker를 활용한 맞춤형 분류 솔루션

## 🎯 핵심 포인트

기존 Apache MXNet 기반 맞춤형 분류 알고리즘을 보유한 상황에서 가장 빠른 NLP 애플리케이션 개발을 원하는 경우, Amazon Comprehend로 전처리하고 AWS Deep Learning Containers with SageMaker로 커스텀 모델을 배포할 수 있다.

## 📝 설명

### Amazon Comprehend + SageMaker가 빠른 개발에 적합한 이유

**Amazon Comprehend**는 관리형 NLP 서비스로 Part-of-Speech 태깅과 핵심 구문 추출을 즉시 사용할 수 있어 전처리 개발 시간을 대폭 단축합니다. **AWS Deep Learning Containers**는 Apache MXNet 환경이 사전 구성되어 있어 기존 모델을 즉시 배포할 수 있습니다.

**핵심 차별점**: Comprehend Custom Classification은 자체 AutoML 방식으로만 훈련되므로, 이미 개발된 MXNet 기반 알고리즘을 활용할 수 없습니다. 따라서 전처리만 Comprehend를 사용하고, 분류는 기존 자산을 그대로 활용하는 것이 최적의 전략입니다.

### 아키텍처 플로우

```
Raw Text Data
    ↓
Amazon Comprehend
├── Part-of-Speech Tagging
└── Key Phrase Extraction
    ↓
Preprocessed Text
    ↓
AWS Deep Learning Containers (MXNet)
├── Existing Custom Classification Algorithm
└── SageMaker Endpoints
    ↓
Classification Results
```

### Trade-offs 고려사항

**Amazon Comprehend 장점**:
- 관리형 서비스로 즉시 사용 가능
- 높은 정확도의 전처리 기능
- 인프라 관리 불필요
- 다양한 언어 지원

**Amazon Comprehend 단점**:
- 커스텀 분류기는 자체 방식으로만 훈련됨
- 기존 MXNet 모델 가져오기 불가능
- API 호출 비용 발생

**SageMaker Built-in 알고리즘 장점**:
- 최적화된 성능
- 자동 하이퍼파라미터 튜닝
- 간편한 배포

**SageMaker Built-in 알고리즘 단점**:
- 기존 개발 자산 활용 불가
- 새로운 모델 훈련 시간 필요
- 특정 알고리즘에 제한됨

## 🔍 주요개념

### 서비스별 분류 모델 지원 방식

- **Amazon Comprehend Custom Classification**: 자체 AutoML 방식, 기존 모델 Import 불가
- **SageMaker Built-in Algorithms**: 사전 정의된 알고리즘 (XGBoost, Linear Learner 등)
- **SageMaker Custom Algorithms**: 사용자 정의 알고리즘, 모든 프레임워크 지원

### 실전 적용

- 금융 문서 분류에서 기존 MXNet 기반 리스크 분류 모델 활용
- 의료 텍스트 분석에서 전문 용어 추출은 Comprehend, 진단 분류는 커스텀 모델 사용
- 고객 리뷰 분석에서 감정 분석 전처리 후 맞춤형 카테고리 분류 적용

## 📝 관련 문제

**Question:** A data science team is developing an NLP application. The text preprocessing will include part-of-speech labeling and key phrase extraction. The preprocessed text will be fed into a custom classification algorithm developed and trained by the team using Apache MXNet. Which solution can the team develop the FASTEST to satisfy these requirements?

**Options:**

- A) Use Amazon Comprehend for the part-of-speech tagging, key phrase extraction, and classification tasks
- B) Use an NLP library in Amazon SageMaker for the part-of-speech tagging. Use Amazon Comprehend for the key phrase extraction. Use AWS Deep Learning Containers with Amazon SageMaker to build the custom classifier
- C) Use Amazon Comprehend for the part-of-speech tagging and key phrase extraction tasks. Use Amazon SageMaker built-in Latent Dirichlet Allocation (LDA) algorithm to build the custom classifier
- D) Use Amazon Comprehend for the part-of-speech tagging and key phrase extraction tasks. Use AWS Deep Learning Containers with Amazon SageMaker to build the custom classifier

**정답: D) Use Amazon Comprehend for preprocessing and AWS Deep Learning Containers with SageMaker for custom classifier**

💡 추가 설명:

- **Option A** - Comprehend Custom Classification은 기존 Apache MXNet 모델을 가져올 수 없어 개발 자산 활용 불가
- **Option B** - Part-of-speech 태깅을 직접 구현해야 해서 개발 시간이 가장 오래 걸림
- **Option C** - LDA는 SageMaker 내장 알고리즘으로 팀이 개발한 MXNet 기반 커스텀 알고리즘과 다름
- **핵심 포인트** - 기존 개발 자산(MXNet 모델)을 최대한 활용하면서 전처리만 관리형 서비스로 처리하는 것이 가장 빠른 개발 방법