---
title: nps-customer-profitability-business-analytics
created: 2025-08-28
modified: 2025-08-28
tags:
- metric/net-promoter-score
- metric/customer-profitability
- analytics/business-intelligence
- measurement/customer-loyalty
- visualization/kpi-dashboard
aliases: ["NPS", "Net Promoter Score", "Customer Profitability Score"]

---

# NPS와 고객 수익성 지표를 활용한 기업 성과 분석

## 🎯 핵심 포인트

고객 추천 의향과 수익성을 측정해야 하는 경우, Net Promoter Score(NPS) KPI 차트와 Customer Profitability Score KPI 차트를 활용하여 기업의 핵심 성과를 효과적으로 시각화할 수 있다.

## 📝 설명

### Net Promoter Score가 고객 추천 측정에 적합한 이유

NPS는 2003년 Fred Reichheld에 의해 개발된 고객 충성도 측정 지표로, 단 하나의 핵심 질문을 통해 고객의 추천 의향을 정확히 파악합니다.

**핵심 질문**: "이 회사/제품/서비스를 친구나 동료에게 추천할 가능성이 얼마나 되십니까?" (0-10점 척도)

**고객 분류**:
- **추천자(Promoters, 9-10점)**: 적극적 추천 의향, 높은 충성도
- **중립자(Passives, 7-8점)**: 만족하지만 추천 의향 낮음
- **비추천자(Detractors, 0-6점)**: 불만족, 부정적 입소문 위험

### 측정 플로우

```
고객 설문 조사
    ↓
0-10점 척도 응답 수집
    ↓
고객 분류 (추천자/중립자/비추천자)
    ↓
NPS 계산: (추천자 비율 - 비추천자 비율) × 100
    ↓
-100~+100 점수 산출
    ↓
업계 벤치마크와 비교 분석
```

### Trade-offs 고려사항

**NPS 장점**:
- 단순하고 직관적인 측정 방식
- 업계 표준으로 벤치마킹 용이
- 고객 충성도와 비즈니스 성장 예측 가능
- 입소문 마케팅 효과 측정

**NPS 단점**:
- 문화적 차이에 따른 점수 편차 존재
- 단일 질문으로 복합적 만족도 측정 한계
- 수치만으로는 개선 방향 파악 어려움

**Distribution Scatter Chart 장점**:
- 추천율의 분포 패턴 시각화
- 데이터 분산도 확인 가능

**Distribution Scatter Chart 단점**:
- 고객의 실제 추천 의향과 직접적 연관성 부족
- 추천 시스템 성능 측정에 더 적합
- 고객 충성도 예측력 제한적

## 🔍 주요개념

### 핵심 지표 비교

- **NPS (Net Promoter Score)**: 고객이 타인에게 추천할 의향을 측정하는 충성도 지표 (-100~+100)
- **Customer Profitability Score**: 개별 고객이 기업에 가져다주는 순이익을 고객 획득/유지 비용을 차감하여 계산한 수익성 지표
- **Conversion Rate**: 방문자가 고객으로 전환하는 비율 (추천과는 다른 개념)
- **Market Share**: 전체 시장에서 기업이 차지하는 비중 (경쟁 위치 측정)

### 실전 적용

- **연간 보고서**: 고객 추천 의향과 수익성을 통한 기업 성과 종합 평가
- **마케팅 전략**: NPS 추천자 그룹 대상 리퍼럴 프로그램 개발
- **고객 관리**: 고수익 + 고NPS 고객 대상 VIP 서비스 제공
- **제품 개발**: NPS 비추천자 피드백을 통한 제품 개선점 도출

## 📝 관련 문제

**Question:** You work for the data analytics department of your company, where you have been asked to build a visualization of the company's corporate performance for the company's annual report. The visualization needs to demonstrate company performance by showing how likely it is for a customer to recommend your company's products, and how much profit a customer brings to the business net acquisition and retention costs. Which types of charts would you use to create this visualization? (Select TWO)

**Options:**

- A) Use a distribution scatter chart to show the customer recommendation rate.
- B) Use a Conversion Rate KPI chart to show the conversion rate of customers.
- C) Use a Relative Market Share KPI chart to show competitive market share.
- D) Use a Net Promoter Score KPI chart to graph customer recommendations.
- E) Use a Customer Profitability Score KPI chart to show customer profitability.

**정답: D) Net Promoter Score KPI chart, E) Customer Profitability Score KPI chart**

💡 추가 설명:

- **Distribution Scatter Chart (A)** - 추천율 분포만 보여주며, 실제 고객의 추천 의향과 직접적 연관성이 부족. 상품 추천 시스템 성능 측정에 더 적합
- **Conversion Rate KPI (B)** - 방문자의 고객 전환율 측정으로, 기존 고객의 추천 의향과는 다른 개념
- **Market Share KPI (C)** - 경쟁사 대비 시장 점유율로, 개별 고객의 추천 성향이나 수익성과 무관한 지표

---