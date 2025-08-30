# 🚀 AWS SageMaker 빌트인 알고리즘 하이퍼파라미터 완벽 가이드

안녕하세요! 오늘은 AWS SageMaker의 빌트인 알고리즘들의 하이퍼파라미터에 대해 자세히 알아보겠습니다. 

머신러닝 모델을 훈련할 때 가장 중요한 것 중 하나가 바로 **하이퍼파라미터 설정**인데요, 어떤 파라미터가 필수이고 어떤 것이 선택사항인지 정확히 아는 것이 중요합니다! 📊

## 📋 하이퍼파라미터란?

하이퍼파라미터는 모델 훈련 전에 사용자가 직접 설정해야 하는 값들입니다. 크게 두 종류로 나뉩니다:

- **필수 하이퍼파라미터** 🔴: 반드시 설정해야 하는 파라미터 (설정하지 않으면 훈련 실패)
- **선택 하이퍼파라미터** 🟡: 기본값이 있어서 생략 가능한 파라미터

---

## 1️⃣ XGBoost 알고리즘

### 🔴 필수 하이퍼파라미터
- **`num_round`**: 부스팅 라운드 수 (훈련 반복 횟수) - **항상 필수**
- **`num_class`**: 클래스 수 - **다중 클래스 분류**(`multi:softmax` 또는 `multi:softprob`) 시에만 필수

### 🟡 주요 선택 하이퍼파라미터
- `max_depth`: 트리의 최대 깊이 (기본값: 6)
- `eta`: 학습률 (기본값: 0.3)
- `gamma`: 리프 노드 분할을 위한 최소 손실 감소 (기본값: 0)
- `min_child_weight`: 자식 노드에 필요한 최소 가중치 합 (기본값: 1)
- `subsample`: 훈련 샘플 서브샘플링 비율 (기본값: 1)
- `colsample_bytree`: 트리 구성 시 열 서브샘플링 비율 (기본값: 1)
- `objective`: 목적함수 (기본값: reg:squarederror)
- `alpha`: L1 정규화 항 (기본값: 0)
- `lambda`: L2 정규화 항 (기본값: 1)

**💡 팁**: XGBoost에서 `num_round`는 multi:softprob뿐만 아니라 **모든 상황에서 필수**입니다!

---

## 2️⃣ Linear Learner 알고리즘

### 🔴 필수 하이퍼파라미터
- **`predictor_type`**: 예측기 타입 (`binary_classifier`, `multiclass_classifier`, `regressor`)
- **`num_classes`**: 클래스 수 - **다중 클래스 분류**(`multiclass_classifier`) 시에만 필수 (3~1,000,000)

### 🟡 주요 선택 하이퍼파라미터
- `epochs`: 최대 훈련 에포크 수 (기본값: 15)
- `learning_rate`: 학습률 (기본값: auto)
- `mini_batch_size`: 미니 배치 크기 (기본값: 1000)
- `optimizer`: 최적화 알고리즘 (기본값: auto)
- `loss`: 손실 함수 (기본값: auto)
- `l1`: L1 정규화 파라미터 (기본값: auto)
- `wd`: L2 정규화 파라미터 (기본값: auto)
- `use_bias`: 편향 항 사용 여부 (기본값: True)
- `normalize_data`: 피처 정규화 여부 (기본값: True)

---

## 3️⃣ Factorization Machines 알고리즘

### 🔴 필수 하이퍼파라미터
- **`feature_dim`**: 입력 피처 차원수
- **`num_factors`**: 인수분해 차원수
- **`predictor_type`**: 예측기 타입 (`binary_classifier`, `regressor`)

### 🟡 주요 선택 하이퍼파라미터
- `epochs`: 훈련 에포크 수 (기본값: 1)
- `bias_lr`: 편향 학습률 (기본값: 0.1)
- `linear_lr`: 선형 항 학습률 (기본값: 0.01)
- `factors_lr`: 인수 학습률 (기본값: 0.0001)
- `clip_gradient`: 그래디언트 클리핑 임계값 (기본값: -1.0, 무제한)

---

## 4️⃣ K-Means 클러스터링

### 🔴 필수 하이퍼파라미터
- **`feature_dim`**: 입력 피처 차원수
- **`k`**: 클러스터 수

### 🟡 주요 선택 하이퍼파라미터
- `epochs`: 훈련 데이터를 거치는 횟수 (기본값: 1)
- `init_method`: 초기 클러스터 중심점 선택 방법 (기본값: random)
- `mini_batch_size`: 미니 배치 크기 (기본값: 5000)
- `extra_center_factor`: 추가 중심점 계수 (기본값: auto)
- `local_lloyd_max_iter`: Lloyd EM 최대 반복 횟수 (기본값: 300)

---

## 5️⃣ PCA (주성분 분석)

### 🔴 필수 하이퍼파라미터
- **`feature_dim`**: 입력 피처 차원수
- **`num_components`**: 주성분 개수

### 🟡 선택 하이퍼파라미터
- `algorithm_mode`: 알고리즘 모드 (기본값: regular)
- `subtract_mean`: 평균 차감 여부 (기본값: True)
- `extra_components`: 추가 구성요소 수 (기본값: -1, auto)

---

## 6️⃣ Random Cut Forest (이상 탐지)

### 🔴 필수 하이퍼파라미터
- **`feature_dim`**: 입력 피처 차원수

### 🟡 선택 하이퍼파라미터
- `num_samples_per_tree`: 트리당 샘플 수 (기본값: 256)
- `num_trees`: 포레스트 내 트리 수 (기본값: 100)
- `eval_metrics`: 평가 메트릭 (기본값: ["accuracy", "precision_recall_fscore"])

---

## 7️⃣ Neural Topic Model (NTM)

### 🔴 필수 하이퍼파라미터
- **`feature_dim`**: 어휘 크기
- **`num_topics`**: 토픽 수

### 🟡 주요 선택 하이퍼파라미터
- `epochs`: 훈련 에포크 수 (기본값: 50)
- `num_patience_epochs`: 조기 종료 patience (기본값: 3)
- `tolerance`: 수렴 허용 오차 (기본값: 0.001)
- `optimizer`: 최적화기 (기본값: adadelta)
- `learning_rate`: 학습률 (기본값: 0.001)

---

## 8️⃣ LDA (토픽 모델링)

### 🔴 필수 하이퍼파라미터
- **`feature_dim`**: 어휘 크기
- **`num_topics`**: 토픽 수

### 🟡 선택 하이퍼파라미터
- `alpha0`: 토픽-문서 분포의 Dirichlet prior (기본값: 1.0)
- `max_restarts`: 최대 재시작 횟수 (기본값: 9)
- `max_iterations`: 최대 반복 횟수 (기본값: 1000)
- `tol`: 수렴 허용 오차 (기본값: 1e-8)

---

## 9️⃣ Seq2Seq (시퀀스-투-시퀀스)

### 🔴 필수 하이퍼파라미터
- **`vocab_size`**: 어휘 크기
- **`num_layers_encoder`**: 인코더 레이어 수
- **`num_layers_decoder`**: 디코더 레이어 수

### 🟡 주요 선택 하이퍼파라미터
- `rnn_type`: RNN 타입 (기본값: gru)
- `hidden_size`: 은닉 상태 크기 (기본값: 512)
- `epochs`: 훈련 에포크 수 (기본값: 10)
- `learning_rate`: 학습률 (기본값: 0.0003)
- `dropout`: 드롭아웃 비율 (기본값: 0.0)

---

## 🔟 DeepAR 시계열 예측

### 🔴 필수 하이퍼파라미터
- **`context_length`**: 컨텍스트 길이
- **`prediction_length`**: 예측 길이

### 🟡 주요 선택 하이퍼파라미터
- `epochs`: 훈련 에포크 수 (기본값: 100)
- `num_cells`: LSTM 셀 수 (기본값: 40)
- `num_layers`: LSTM 레이어 수 (기본값: 2)
- `likelihood`: 가능도 함수 (기본값: student-T)
- `dropout_rate`: 드롭아웃 비율 (기본값: 0.1)
- `learning_rate`: 학습률 (기본값: 1e-3)

---

## 1️⃣1️⃣ BlazingText (텍스트 분석)

### 🔴 필수 하이퍼파라미터
- **`mode`**: 모드 (`Word2Vec`, `classification`, `supervised`)

### 🟡 Word2Vec 모드 주요 파라미터
- `epochs`: 훈련 에포크 수 (기본값: 5)
- `learning_rate`: 학습률 (기본값: 0.05)
- `vector_dim`: 벡터 차원 (기본값: 100)
- `window_size`: 윈도우 크기 (기본값: 5)
- `min_count`: 최소 단어 출현 빈도 (기본값: 5)

### 🟡 텍스트 분류 모드 주요 파라미터
- `word_ngrams`: 단어 n-gram 수 (기본값: 1)
- `vector_dim`: 벡터 차원 (기본값: 100)

---

## 1️⃣2️⃣ Object2Vec (임베딩)

### 🔴 필수 하이퍼파라미터
- **`enc0_network`**: 첫 번째 입력의 인코더 네트워크 타입
- **`enc1_network`**: 두 번째 입력의 인코더 네트워크 타입

### 🟡 주요 선택 하이퍼파라미터
- `epochs`: 훈련 에포크 수 (기본값: 30)
- `learning_rate`: 학습률 (기본값: 0.001)
- `mlp_layers`: MLP 레이어 수 (기본값: 2)
- `mlp_dim`: MLP 차원 (기본값: 1024)
- `optimizer`: 최적화기 (기본값: adam)

---

## 1️⃣3️⃣ IP Insights (이상 탐지)

### 🔴 필수 하이퍼파라미터
- **`num_entity_vectors`**: 엔티티 벡터 수
- **`vector_dim`**: 벡터 차원수

### 🟡 주요 선택 하이퍼파라미터
- `epochs`: 훈련 에포크 수 (기본값: 5)
- `learning_rate`: 학습률 (기본값: 0.01)
- `batch_size`: 배치 크기 (기본값: 32)

---

## 🎯 핵심 포인트 정리

### ✅ 중요한 수정사항 (AWS 공식 문서 검증)

1. **XGBoost `num_round`**: multi:softprob뿐만 아니라 **모든 상황에서 필수**
2. **Linear Learner `predictor_type`**: **항상 필수**로 설정해야 함
3. **조건부 필수 파라미터들**: 특정 모드나 목적함수 사용 시에만 필수

### 💡 실무 팁

- **하이퍼파라미터 튜닝**: SageMaker Automatic Model Tuning 활용 추천
- **기본값 활용**: 처음에는 기본값으로 시작한 후 점진적으로 튜닝
- **문서 확인**: AWS 공식 문서에서 최신 정보 확인 필수

---

## 🔗 참고 자료

이 가이드는 AWS 공식 문서를 기반으로 작성되었으며, 실제 운영 환경에서 검증된 정보입니다. 

SageMaker를 활용한 머신러닝 프로젝트에 도움이 되길 바랍니다! 🚀

질문이나 궁금한 점이 있으시면 댓글로 남겨주세요! 😊

---

## 📱 해시태그

#AWS #SageMaker #머신러닝 #하이퍼파라미터 #XGBoost #LinearLearner #클라우드ML #데이터사이언스 #AI개발 #MLOps