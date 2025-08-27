---

title: ml-visualization-degradation-analysis
created: 2025-08-27
modified: 2025-08-27  
tags:
- visualization/line-chart
- ml/model-degradation
- metrics/mae
- analysis/time-series
- aws/sagemaker
aliases: ["ml-viz", "model-perf", "degradation"]

---

# ML 모델 성능 저하 시각화 - 시간별 MAE 추세 분석

## 🎯 핵심 포인트

모델 성능 저하(degradation)를 분석하는 경우 시간에 따른 MAE(평균 절대 오차) 변화를 선 그래프로 시각화할 수 있다.

## 📝 설명

### 시각화 타입별 목적과 적용 사례

**선 그래프 (Line Chart)**
- **목적**: 시간에 따른 변화 추세 파악
- **보여주는 것**: "언제부터 성능이 저하되기 시작했는가?"
- **적용**: 모델 성능 모니터링, 시계열 데이터 분석

**히스토그램 (Histogram)**  
- **목적**: 데이터의 분포 파악
- **보여주는 것**: "값들이 어떤 범위에 몰려있는가?"
- **적용**: 데이터 분포 확인, 이상치 탐지

**산점도 (Scatter Plot)**
- **목적**: 두 변수 간의 관계/상관성 파악  
- **보여주는 것**: "A가 클 때 B도 큰가? 어떤 조건에서 문제가 발생하는가?"
- **적용**: 원인 분석, 특정 조건별 성능 차이 분석

### MAE를 활용한 성능 저하 시각화

**MAE (Mean Absolute Error) = |실제값 - 예측값|의 평균**

```
주간 MAE 추세
MAE ↑
 10 |     ●       ← 4주차: 심각한 성능 저하
    |    /
  8 |   ●         ← 3주차: 성능 저하 시작  
    |  /
  6 | ●           ← 2주차: 약간의 성능 저하
    |/
  4 ●─────────────→ 시간
    1주 2주 3주 4주
```

### Trade-offs 고려사항

**선 그래프 장점**:
- 시간에 따른 명확한 추세 파악 가능
- 성능 저하 시점을 정확히 식별
- 저하 패턴(점진적 vs 급격한) 구분 가능
- 직관적이고 이해하기 쉬움

**선 그래프 단점**:
- 성능 저하의 근본 원인은 알려주지 않음
- 단순 추세만 보여줄 뿐 세부 분석 제한적

**산점도 장점**:
- 특정 조건에서의 성능 차이 발견 가능
- 이상치와 패턴을 동시에 식별
- 모델의 약점과 강점 구간 파악

**산점도 단점**:
- 시간적 변화를 직접적으로 보여주지 못함
- 성능 저하 시점 파악 어려움

## 🔍 주요개념

### 시각화 타입 선택 가이드

- **"시간에 따른 변화"가 궁금하면** → 선 그래프
- **"두 변수의 관계"가 궁금하면** → 산점도  
- **"데이터 분포"가 궁금하면** → 히스토그램

### 실전 적용

- **성능 모니터링**: 주간/월간 MAE 추세로 모델 건강도 체크
- **원인 분석**: 매출 규모 vs 예측 오차 산점도로 특정 조건에서의 약점 파악
- **이상치 탐지**: 예측값 vs 실제값 산점도에서 비정상적 케이스 발견

## 📝 관련 문제

**Question:** A retail company uses a machine learning model for daily sales forecasting. The brand manager reports that the model has provided inaccurate results for the past 3 weeks. What should the ML team do on the SageMaker Studio notebook to visualize the model's degradation MOST accurately?

**Options:**

- A) Create a histogram of the daily sales over the last 3 weeks. In addition, create a histogram of the daily sales from before that period.
- B) Create a histogram of the model errors over the last 3 weeks. In addition, create a histogram of the model errors from before that period.  
- C) Create a line chart with the weekly mean absolute error (MAE) of the model.
- D) Create a scatter plot of daily sales versus model error for the last 3 weeks. In addition, create a scatter plot of daily sales versus model error from before that period.

**정답: C) Create a line chart with the weekly mean absolute error (MAE) of the model**

💡 추가 설명:

- **A 옵션** - 실제 매출 분포만 보여줄 뿐 모델 성능과는 무관
- **B 옵션** - 오차 분포는 알 수 있지만 시간에 따른 변화 추세 파악 불가
- **D 옵션** - 매출과 오차의 관계는 보여주지만 언제부터 성능이 저하되었는지 알 수 없음

**핵심**: "degradation"이라는 키워드는 시간에 따른 성능 **변화 과정**을 의미하므로 시간축이 있는 시각화(선 그래프)가 필요