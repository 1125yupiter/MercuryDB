# 대화 정리 프롬프트 (ML 전체 커버리지 버전)

대화 내용을 아래 템플릿으로 정리해줘. '설명' 섹션은 내용에 맞게 자유롭게 구성 가능.

## 📋 필수 템플릿

```markdown
---
title: [태그-태그-태그.md 형식]
created: 2025-08-15
modified: 2025-08-15
tags:
- category/value (5개 이상, 내용에 따라 조정)
aliases: ["별칭1", "별칭2"] # 영어만
---

# [주제 제목 - 핵심 개념/기술과 사용 사례 포함]

## 🎯 핵심 포인트
[~한 경우 ~에서, ~할 수 있다 형식 한 문장]

## 📝 설명

### [주요 내용에 맞는 섹션 구성]
[핵심 특징과 작동 방식 설명]

### 아키텍처/프로세스 플로우
```
[시스템 플로우 또는 프로세스 시각화]
```

### Trade-offs 고려사항
**장점**:
- [장점 나열]

**단점/한계**:
- [단점 나열]

**대안 비교**:
- [대안과 비교 분석]

## 🔍 주요개념

### 핵심 개념 정리
- **[개념 A]**: [정의와 특징]
- **[개념 B]**: [정의와 특징]

### 실전 적용
- [구체적 시나리오들]

## 📝 관련 문제
**Question:** [시나리오 기반 문제, 영어]

**Options:**
- A) ~ E) [선택지]

**정답: [옵션]) [솔루션]**

💡 추가 설명:
- [각 옵션별 설명]
```

## 🏷️ 확장된 태그 카테고리 (ML 전체 커버리지)

### 1. **domain** (ML 도메인)
- `ml/supervised`, `ml/unsupervised`, `ml/reinforcement`
- `ml/deep-learning`, `ml/classical`, `ml/ensemble`
- `nlp/generation`, `nlp/classification`, `nlp/extraction`
- `cv/detection`, `cv/segmentation`, `cv/generation`
- `timeseries/forecasting`, `timeseries/anomaly`

### 2. **problem** (문제 유형)
- `problem/classification`, `problem/regression`, `problem/clustering`
- `problem/anomaly`, `problem/forecasting`, `problem/ranking`
- `problem/recommendation`, `problem/optimization`
- `problem/generation`, `problem/extraction`

### 3. **algorithm** (알고리즘/모델)
- `algorithm/transformer`, `algorithm/cnn`, `algorithm/rnn`, `algorithm/gnn`
- `algorithm/xgboost`, `algorithm/random-forest`, `algorithm/svm`
- `algorithm/kmeans`, `algorithm/dbscan`, `algorithm/pca`
- `algorithm/bert`, `algorithm/gpt`, `algorithm/diffusion`

### 4. **technique** (기법/방법론)
- `technique/fine-tuning`, `technique/transfer-learning`, `technique/few-shot`
- `technique/data-augmentation`, `technique/feature-engineering`
- `technique/ensemble`, `technique/hyperparameter-tuning`
- `technique/quantization`, `technique/distillation`, `technique/pruning`
- `technique/rag`, `technique/prompt-engineering`, `technique/chain-of-thought`

### 5. **infrastructure** (인프라/도구)
- `infra/training`, `infra/inference`, `infra/deployment`
- `infra/distributed`, `infra/edge`, `infra/cloud`
- `tool/pytorch`, `tool/tensorflow`, `tool/scikit-learn`
- `tool/huggingface`, `tool/langchain`, `tool/mlflow`
- `service/sagemaker`, `service/vertex-ai`, `service/azure-ml`

### 6. **data** (데이터 관련)
- `data/structured`, `data/unstructured`, `data/multimodal`
- `data/tabular`, `data/text`, `data/image`, `data/audio`, `data/video`
- `data/streaming`, `data/batch`, `data/real-time`
- `data/small-scale`, `data/large-scale`, `data/big-data`

### 7. **challenge** (도전과제/제약)
- `challenge/overfitting`, `challenge/underfitting`
- `challenge/imbalanced`, `challenge/missing-data`, `challenge/noisy`
- `challenge/interpretability`, `challenge/fairness`, `challenge/privacy`
- `challenge/latency`, `challenge/throughput`, `challenge/cost`
- `challenge/drift`, `challenge/catastrophic-forgetting`

### 8. **stage** (ML 파이프라인 단계)
- `stage/data-collection`, `stage/preprocessing`, `stage/eda`
- `stage/feature-engineering`, `stage/training`, `stage/validation`
- `stage/evaluation`, `stage/deployment`, `stage/monitoring`
- `stage/retraining`, `stage/a-b-testing`

### 9. **metric** (평가지표)
- `metric/accuracy`, `metric/precision`, `metric/recall`, `metric/f1`
- `metric/auc-roc`, `metric/rmse`, `metric/mae`, `metric/perplexity`
- `metric/bleu`, `metric/rouge`, `metric/map`, `metric/ndcg`

### 10. **industry** (산업/응용분야)
- `industry/healthcare`, `industry/finance`, `industry/retail`
- `industry/manufacturing`, `industry/autonomous`, `industry/security`
- `usecase/fraud-detection`, `usecase/recommendation`, `usecase/chatbot`

## 📝 파일명 규칙 예시

### ML 이론/개념
```
overfitting-regularization-techniques.md
transformer-attention-mechanism.md
gradient-descent-optimization.md
```

### 실전/구현
```
bert-finetuning-classification.md
rag-implementation-langchain.md
distributed-training-pytorch.md
```

### 문제해결
```
imbalanced-data-sampling-techniques.md
model-drift-monitoring-strategies.md
latency-optimization-inference.md
```

### 비교/선택
```
compare-cnn-transformer-vision.md
choosing-embedding-models.md
batch-vs-streaming-ml.md
```

## ✅ 작성 체크리스트
- [ ] 핵심 포인트가 "~한 경우 ~에서, ~할 수 있다" 형식인가?
- [ ] 태그가 내용의 다양한 측면을 커버하는가? (도메인, 기법, 도전과제 등)
- [ ] ML 파이프라인의 어느 단계에 해당하는지 명확한가?
- [ ] 실전 시나리오가 구체적인가?
- [ ] 관련 개념들이 잘 연결되어 있는가?

---

**사용법**: 대화 내용을 이 템플릿에 맞춰 정리. ML의 다양한 측면(이론, 구현, 도구, 도전과제 등)을 태그로 표현.