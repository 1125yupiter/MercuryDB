---

title: f1-score-confusion-matrix-evaluation
created: 2025-08-18 
modified: 2025-08-18 
tags:
- evaluation/f1-score
- metrics/classification
- matrix/confusion
- performance/binary
- calculation/precision-recall
aliases: ["f1score", "confusion-matrix", "classification-metrics"]

---

# F1 Score와 Confusion Matrix를 이용한 분류 성능 평가

## 🎯 핵심 포인트

이진 분류 모델의 성능을 평가하는 경우 Confusion Matrix에서 Precision과 Recall을 계산하여, F1 Score로 균형잡힌 성능 지표를 얻을 수 있다.

## 📝 설명

### F1 Score가 분류 성능 평가에 적합한 이유

F1 Score는 Precision과 Recall의 조화평균으로, 두 지표 간의 균형을 제공합니다. 특히 클래스 불균형 상황에서 단순 정확도보다 더 신뢰할 수 있는 평가 지표입니다. Precision은 모델이 Positive로 예측한 것 중 실제로 Positive인 비율이고, Recall은 실제 Positive 중에서 모델이 올바르게 예측한 비율입니다.

### Confusion Matrix 해석 플로우

```
실제값
       P    N
예측 P [TP] [FP]  ← Precision = TP/(TP+FP)
    N [FN] [TN]
      ↑
   Recall = TP/(TP+FN)

F1 Score = 2 × (Precision × Recall) / (Precision + Recall)
```

### 계산 과정 상세

**주어진 Confusion Matrix:**
```
       실제값
       P    N
예측 P  8    2   (TP=8, FP=2)
    N  4    6   (FN=4, TN=6)
```

**단계별 계산:**
1. **Precision** = TP/(TP+FP) = 8/(8+2) = 8/10 = 0.8
2. **Recall** = TP/(TP+FN) = 8/(8+4) = 8/12 = 0.667
3. **F1 Score** = 2×(0.8×0.667)/(0.8+0.667) = 2×0.5336/1.467 ≈ **0.73**

## 🔍 주요개념

### 핵심 지표 비교

- **Precision (정밀도)**: 모델이 Positive로 예측한 것 중 실제 Positive 비율 - False Positive를 얼마나 줄였는가
- **Recall (재현율)**: 실제 Positive 중 모델이 올바르게 찾아낸 비율 - False Negative를 얼마나 줄였는가
- **F1 Score**: Precision과 Recall의 조화평균 - 두 지표의 균형점

### 실전 적용

- **의료 진단**: 질병 진단에서 False Negative(놓친 환자)를 줄이려면 Recall 중시
- **스팸 필터링**: False Positive(정상 메일을 스팸 처리)를 줄이려면 Precision 중시  
- **사기 탐지**: 두 오류가 모두 중요한 경우 F1 Score로 균형잡힌 평가

## 📝 관련 문제

**Question:** What is the F1 score of the confusion matrix below? (columns represent actual values, and rows predicted values)

```
8  2
4  6
```

**Options:**

- A) 0.36
- B) 0.6
- C) 0.67
- D) 0.73
- E) 0.8

**정답: D) 0.73**

💡 추가 설명:

- **A) 0.36** - 잘못된 계산 또는 다른 지표와 혼동
- **B) 0.6** - Recall 값과 혼동 (8/12 ≠ 0.6, 실제로는 0.667)
- **C) 0.67** - Recall 값 (8/12 = 0.667)을 F1 Score로 잘못 인식
- **E) 0.8** - Precision 값 (8/10 = 0.8)을 F1 Score로 잘못 인식