---

title: xgboost-multiclass-softprob-hyperparameters
created: 2025-08-28
modified: 2025-08-28
tags:
- algorithm/xgboost
- problem/multiclass-classification
- technique/hyperparameter-tuning
- objective/softprob
- usecase/marketing-campaign
aliases: ["XGBoost multiclass", "softprob", "multi:softprob"]

---

# XGBoost 다중 클래스 분류에서 multi:softprob 목적함수 활용

## 🎯 핵심 포인트

XGBoost에서 다중 클래스 분류를 수행하며 각 클래스에 대한 확률값이 필요한 경우, multi:softprob 목적함수를 사용할 때 num_round와 num_class 파라미터를 반드시 설정해야 한다.

## 📝 설명

### multi:softprob가 마케팅 캠페인 예측에 적합한 이유

multi:softprob는 XGBoost의 다중 클래스 분류 목적함수로, 각 클래스에 대한 확률값을 출력합니다. 마케팅 분야에서는 단순히 "반응할 것이다/안 할 것이다"보다는 "85% 확률로 반응할 것이다"라는 정보가 훨씬 유용합니다. 이를 통해 리스크 평가, 개인화된 추천, A/B 테스트 등에서 확률 기반 의사결정을 할 수 있습니다.

### 아키텍처 플로우

```
고객 데이터 입력
    ↓
XGBoost 모델 (multi:softprob)
    ↓
softmax 함수 적용
    ↓
각 클래스별 확률 출력 [0.7, 0.2, 0.1]
    ↓
확률 기반 의사결정 (임계값 조정)
    ↓
마케팅 액션 실행
```

### Trade-offs 고려사항

**multi:softprob 장점**:
- 각 클래스에 대한 확률값 제공으로 신뢰도 평가 가능
- 확률 기반 의사결정 지원 (임계값 조정 가능)
- 리스크 관리 및 개인화에 효과적
- A/B 테스트와 성과 측정에 유리

**multi:softprob 단점**:
- 계산 복잡도가 multi:softmax보다 높음
- 메모리 사용량 증가 (클래스 수만큼 확률값 저장)
- 해석이 상대적으로 복잡함

**multi:softmax 장점**:
- 단순한 분류 결과만 필요한 경우 효율적
- 메모리 사용량 적음
- 빠른 예측 속도

**multi:softmax 단점**:
- 확률 정보 제공 안함
- 모델의 신뢰도 평가 어려움
- 임계값 조정 불가능

## 🔍 주요개념

### 목적함수 비교

- **multi:softprob**: 확률값 출력 [0.7, 0.2, 0.1], softmax 함수 사용, 확률 합계 = 1
- **multi:softmax**: 클래스 레이블 출력 (0, 1, 2), 단순 분류 결과만 제공

### 필수 하이퍼파라미터

- **num_class**: 분류할 클래스의 총 개수를 지정 (기본값 없음, 필수 설정)
- **num_round**: 부스팅 라운드 수, 트리 개수 결정 (기본값 없음, 필수 설정)

### 실전 적용

- **고객 반응 예측**: "이 고객이 캠페인에 반응할 확률이 85%"
- **세그먼테이션**: 확률 높은 순으로 고객 그룹 분류 및 차별화된 전략 수립
- **리스크 관리**: 확률 기반으로 마케팅 비용 대비 효과 최적화

## 📝 관련 문제

**Question:** You work as a machine learning specialist for a marketing firm. Your firm wishes to determine which customers in a dataset of its registered users will respond to a new proposed marketing campaign. You plan to use the XGBoost algorithm on the binary classification problem. In order to find the optimal model, you plan to run many hyperparameter tuning jobs to reach the best hyperparameter values. Which of the following hyperparameters must you use in your tuning jobs if your objective is set to multi:softprob? (Select TWO)

**Options:**

- A) alpha
- B) base_score  
- C) eta
- D) num_round
- E) gamma
- F) num_class

**정답: D) num_round, F) num_class**

💡 추가 설명:

- **A) alpha** - L1 정규화 파라미터로 과적합 방지에 도움되지만 기본값(0)이 있어 필수가 아님
- **B) base_score** - 초기 예측값 설정 파라미터로 기본값(0.5)이 있어 필수가 아님  
- **C) eta** - 학습률 조절 파라미터로 성능에 중요하지만 기본값(0.3)이 있어 필수가 아님
- **E) gamma** - 트리 분할 최소 손실 감소 임계값으로 정규화에 사용되지만 기본값(0)이 있어 필수가 아님

---