---

title: overfitting-regularization-depression
created: 2025-08-17
modified: 2025-08-17
tags:
- problem/overfitting
- technique/regularization
- application/depression-detection
- domain/healthcare
- method/deep-learning
aliases: ["overfitting", "regularization", "L1-L2"]

---

# 딥러닝 모델의 과적합 문제와 정규화 기법 - 우울증 감지 사례

## 🎯 핵심 포인트

훈련 에러가 지수적으로 감소하는 경우 과적합 상태에서, L2 정규화를 통해 모델의 일반화 성능을 향상시킬 수 있다.

## 📝 설명

### 과적합(Overfitting) 진단과 해결이 우울증 감지 모델에 적합한 이유

헬스케어 회사의 얼굴 랜드마크 기반 우울증 감지 모델에서 에폭이 증가할수록 훈련 에러가 지수적으로 감소하는 현상은 전형적인 과적합 패턴입니다. 2만 개의 이미지 데이터로 훈련된 모델이 훈련 데이터를 "암기"하면서 새로운 데이터에 대한 일반화 능력을 잃게 됩니다.

### 과적합 발생 메커니즘

```
데이터 입력 → 신경망 학습 → 패턴 인식 단계별 과적합
     ↓              ↓              ↓
초기: 일반 패턴  →  중기: 세부 패턴  →  후기: 노이즈 암기
(에러 20%)     (에러 5%)        (에러 0.1%)
     ↓              ↓              ↓
검증 성능 양호  →  검증 성능 정체  →  검증 성능 악화
```

### Trade-offs 고려사항

**L2 정규화 장점**:
- 모든 가중치를 균등하게 축소하여 안정적인 성능 확보
- 얼굴 랜드마크처럼 모든 특성이 중요한 경우에 적합
- 가중치의 상관관계가 높을 때 안정성 제공
- 구현이 간단하고 하이퍼파라미터 튜닝이 용이

**L2 정규화 단점**:
- 특성 선택 효과가 없어 모델 크기 감소 불가
- 불필요한 특성이 많은 경우 효율성 떨어짐

**L1 정규화 장점**:
- 불필요한 특성을 완전히 제거하여 희소 모델 생성
- 특성 선택 효과로 해석 가능성 향상
- 메모리 사용량 감소

**L1 정규화 단점**:
- 얼굴 랜드마크처럼 모든 특성이 중요한 경우 성능 저하 위험
- 중요한 특성까지 제거할 가능성
- 불안정한 특성 선택 결과

## 🔍 주요개념

### 과적합 vs 언더피팅 비교

- **언더피팅**: 훈련 데이터에서도 성능이 나쁨 (훈련 ❌ 검증 ❌) - 모델이 너무 단순
- **적정 피팅**: 훈련과 검증 성능이 비슷하게 좋음 (훈련 ✅ 검증 ✅) - 이상적 상태
- **과적합**: 훈련은 좋지만 검증이 나쁨 (훈련 ✅ 검증 ❌) - 모델이 너무 복잡

### 실전 적용

- **의료 이미지 분석**: MRI, X-ray 등에서 L2 정규화로 안정적인 진단 모델 구축
- **자연어 처리**: 감정 분석 모델에서 모든 단어 특성이 중요한 경우 L2 적용
- **추천 시스템**: 사용자-아이템 상호작용 데이터에서 L2로 과적합 방지

## 📝 관련 문제

**Question:** A healthcare company is developing a supplementary analysis tool to detect if a person is suffering from depression by analyzing facial landmarks. The company provides 20,000 image datasets and uses machine learning experts to train the model. Initial model analysis found that as the number of epochs increases, the training error decreases exponentially, leading to poor generalization. What should be done to improve generalization?

**Options:**

- A) Increase the number of hidden layers of the neural network
- B) Insert more domain-specific features into the dataset  
- C) Apply L2 regularization
- D) Use normalization on the image data
- E) Increase the learning rate

**정답: C) Apply L2 regularization**

💡 추가 설명:

- **A) 히든레이어 증가** - 모델 복잡도만 더 높여서 과적합을 악화시킴
- **B) 도메인 특성 추가** - 이미 과적합 상태에서 더 많은 특성 추가는 문제를 악화
- **D) 이미지 정규화** - 데이터 분포 개선 효과는 있지만 과적합 근본 해결책 아님  
- **E) 학습률 증가** - 훈련 불안정성만 증가시키고 과적합 해결에 도움 안됨