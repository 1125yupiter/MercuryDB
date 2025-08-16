---

title: confusion-matrix-multiclass-visualization
created: 2025-08-16
modified: 2025-08-16
tags:
- visualization/confusion-matrix
- classification/multi-class
- evaluation/performance
- machine-learning/metrics
- model/movie-genre
aliases: ["confusion-matrix", "multiclass-evaluation", "performance-visualization"]

---

# Confusion Matrix를 활용한 다중 분류 모델 성능 시각화

## 🎯 핵심 포인트

다중 분류 모델의 성능을 평가할 때 Confusion Matrix를 사용하면, 각 클래스별 예측 정확도와 오분류 패턴을 시각적으로 한눈에 파악할 수 있다.

## 📝 설명

### Confusion Matrix가 다중 분류 성능 평가에 적합한 이유

Confusion Matrix는 이진 분류뿐만 아니라 다중 분류에서도 사용 가능한 강력한 시각화 도구입니다. 영화 장르 예측과 같은 다중 분류 문제에서는 N개의 클래스에 대해 N×N 매트릭스를 생성하여 각 실제 클래스가 어떤 클래스로 예측되었는지를 보여줍니다.

**모델 예측 과정:**
- 입력: 영화 메타데이터, 줄거리, 시놉시스 등
- 출력: 각 장르별 확률 (예: SF 85%, 드라마 70%, 액션 30%)
- 분류: 임계값(보통 50%) 이상인 장르들로 최종 분류

### 아키텍처 플로우

```
영화 데이터 입력
    ↓
다중 분류 모델 (ML Model)
    ↓
장르별 확률 출력 (SF: 85%, 드라마: 70%, 액션: 30%, ...)
    ↓
임계값 적용 (Threshold: 50%)
    ↓
최종 장르 분류 (SF + 드라마)
    ↓
실제 장르와 비교
    ↓
Confusion Matrix 생성 (N×N 매트릭스)
    ↓
히트맵으로 시각화 (색상으로 성능 표현)
```

### Trade-offs 고려사항

**Confusion Matrix 장점**:
- 클래스별 세부 성능을 동시에 파악 가능
- 오분류 패턴을 시각적으로 명확히 드러냄
- 다양한 메트릭(정확도, 재현율, F1-score) 계산 기반 제공
- 모델 개선 방향을 구체적으로 제시

**Confusion Matrix 한계**:
- 클래스 수가 많아질수록 매트릭스가 복잡해짐
- 클래스 불균형 시 해석에 주의 필요
- 정적 시각화로 시간에 따른 변화 추적 어려움

**Box Plot 한계**:
- 연속형 데이터 분포 시각화 용도로 범주형 분류 결과에 부적합
- 클래스별 성능 비교 불가능

**Correlation Matrix 한계**:
- 변수 간 상관관계 분석 도구로 분류 성능 평가와 무관
- 예측 정확도나 오분류 패턴 파악 불가

**Scatterplot 한계**:
- 두 연속형 변수 관계 시각화 용도
- 다중 클래스 분류 결과의 전체적 패턴 파악 제한적

## 🔍 주요개념

### 이진 vs 다중 분류에서 Confusion Matrix

- **이진 분류 (2×2)**: True/False, Positive/Negative 개념으로 TP, TN, FP, FN
- **다중 분류 (N×N)**: 각 셀은 "실제 클래스 i를 클래스 j로 예측한 빈도"를 나타냄

### 실전 적용

- **영화 스트리밍 서비스**: 사용자 취향 기반 장르 추천 시스템 성능 평가
- **콘텐츠 자동 태깅**: 업로드된 영화 콘텐츠의 자동 장르 분류 시스템
- **마케팅 타겟팅**: 장르별 관객층 분석을 통한 마케팅 전략 수립

## 📝 관련 문제

**Question:** A Machine Learning Specialist is training a Multi Classification model to predict movie genres. The Specialist wants to evaluate model performance through a visual representation of different metrics. Which visualization technique should the Specialist use?

**Options:**

- A) Box plot
- B) Correlation matrix  
- C) Confusion matrix
- D) Scatterplot

**정답: C) Confusion matrix**

💡 추가 설명:

- **Box plot** - 연속형 데이터의 분포와 이상치를 시각화하는 도구로, 범주형 분류 결과 평가에는 부적합
- **Correlation matrix** - 변수 간 선형 상관관계를 보여주는 도구로, 분류 모델의 성능 평가와는 무관
- **Scatterplot** - 두 연속형 변수의 관계를 보여주지만, 다중 클래스 분류의 전체적인 성능 패턴을 파악하기에는 제한적

---