---

title: correlation-social-media-academic-performance
created: 2025-08-17
modified: 2025-08-17
tags:
- statistics/correlation
- analysis/pearson
- research/academic
- variable/negative
- interpretation/moderate
aliases: ["correlation-coefficient", "pearson-correlation", "social-media-study"]

---

# 상관계수 분석 - 소셜미디어 사용 시간과 학업 성취도 관계

## 🎯 핵심 포인트

상관계수가 -0.68인 경우 데이터 사이언스 분석에서, 두 변수 간에 상당히 강한 음의 상관관계가 존재한다고 해석할 수 있다.

## 📝 설명

### 상관계수가 데이터 분석에 적합한 이유

상관계수(Correlation Coefficient)는 두 변수 간의 선형 관계의 강도와 방향을 측정하는 통계적 지표입니다. 특히 피어슨 상관계수(Pearson Correlation Coefficient)는 -1부터 +1 사이의 값을 가지며, 변수 간의 관계를 정량적으로 분석할 수 있게 해줍니다.

### 분석 플로우

```
데이터 수집
    ↓
변수 정의 (X: 소셜미디어 사용시간, Y: 학업성취도)
    ↓
상관계수 계산 (r = -0.68)
    ↓
관계 강도 해석 (중간~강한 음의 상관관계)
    ↓
결론 도출 (소셜미디어 사용 증가 → 학업성취도 감소)
```

### Trade-offs 고려사항

**상관분석의 장점**:
- 두 변수 간 관계의 강도를 정량적으로 측정
- 방향성(양/음)을 명확히 파악 가능
- 비교적 간단한 계산으로 의미 있는 인사이트 제공
- 다양한 분야에서 널리 활용되는 표준 방법

**상관분석의 단점**:
- 상관관계가 인과관계를 의미하지 않음
- 비선형 관계는 제대로 포착하지 못함
- 이상치(outlier)에 민감함
- 제3의 변수(confounding variable) 영향을 고려하지 않음

**회귀분석의 장점**:
- 예측 모델링 가능
- 인과관계 추론에 더 적합
- 여러 독립변수 동시 고려 가능

**회귀분석의 단점**:
- 더 복잡한 분석 과정 필요
- 가정 조건들을 만족해야 함
- 해석이 상대적으로 복잡

## 🔍 주요개념

### 상관계수 강도 분류

- **강한 상관관계**: |r| ≥ 0.7 (매우 밀접한 관계)
- **중간 상관관계**: 0.3 ≤ |r| < 0.7 (적당한 관계)
- **약한 상관관계**: |r| < 0.3 (미약한 관계)
- **무상관**: r ≈ 0 (관계 없음)

### 실전 적용

- **교육 정책 수립**: 학생들의 소셜미디어 사용 가이드라인 개발
- **학습 환경 개선**: 디지털 디톡스 프로그램 도입 검토
- **학부모 상담**: 자녀의 스마트폰 사용 시간 관리 전략 수립

## 📝 관련 문제

**Question:** A Data Science team is conducting research on the effects of social media on the academic performance of high school students. The team will graph the results with two variables: Average hours spent on using social media on the x-axis and Student's GPA on the y-axis. The resulting data shows a -0.68 correlation coefficient. What does this value indicate?

**Options:**

- A) The amount of time spent on social media has no relationship with the student's performance.
- B) Students perform slightly better the longer time they are exposed to social media.
- C) Students perform considerably poorer the longer time they are exposed to social media.
- D) Students perform strongly better the longer time they are exposed to social media.

**정답: C) Students perform considerably poorer the longer time they are exposed to social media.**

💡 추가 설명:

- **Option A** - 상관계수가 0에 가까울 때 해당하는 해석으로, -0.68은 명확한 관계를 나타냄
- **Option B** - 약한 양의 상관관계(+0.1~+0.3)일 때의 해석으로, 음수 값과 모순됨
- **Option D** - 강한 양의 상관관계(+0.7 이상)일 때의 해석으로, 부호가 반대임

### 상관계수 해석 가이드

**-0.68의 의미**:
- **부호(-)**: 음의 상관관계 → 한 변수 증가 시 다른 변수 감소
- **크기(0.68)**: 중간~강한 수준의 상관관계 → 상당한 영향력 존재
- **실무적 해석**: 소셜미디어 사용 시간이 늘어날수록 학업 성취도가 상당히 감소하는 경향

**주의사항**:
- 상관관계 ≠ 인과관계 (다른 요인들의 영향 가능)
- 개별 학생에게는 다른 패턴이 나타날 수 있음
- 추가적인 통계 분석(회귀분석, 매개효과 분석 등)이 필요할 수 있음