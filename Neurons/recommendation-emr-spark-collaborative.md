---

title: recommendation-emr-spark-collaborative
created: 2025-08-18
modified: 2025-08-18
tags:
- problem/recommendation
- service/emr
- technique/collaborative-filtering
- constraint/similarity-based
- usecase/sales-boost
aliases: ["collab-filtering", "spark-rec", "emr-ml"]

---

# Amazon EMR Spark를 활용한 협업 필터링 추천 시스템

## 🎯 핵심 포인트

고객 간 유사성 패턴을 기반으로 제품 추천을 통해 매출을 증대하고자 하는 경우 Amazon EMR에서 Apache Spark를 사용하여 협업 필터링 추천 엔진을 구축할 수 있다.

## 📝 설명

### Apache Spark on EMR이 추천 시스템에 적합한 이유

Amazon EMR은 Apache Spark를 실행하기 위한 최적의 관리형 환경을 제공하며, Spark의 MLlib 라이브러리를 통해 대규모 협업 필터링 알고리즘을 효율적으로 구현할 수 있습니다. 협업 필터링은 (사용자, 아이템, 평점) 튜플을 기반으로 작동하며, 비슷한 취향을 가진 사용자들이 아직 경험하지 못한 제품에 대해서도 유사한 선호도를 보일 것이라는 개념에 기반합니다.

### 아키텍처 플로우

```
사용자 행동 데이터 → S3 스토리지
                    ↓
EMR 클러스터 (Apache Spark + MLlib)
                    ↓
협업 필터링 모델 훈련 (ALS 알고리즘)
                    ↓
사용자-제품 유사도 매트릭스 생성
                    ↓
실시간 추천 API (Lambda + API Gateway)
                    ↓
고객에게 개인화된 제품 추천 제공
```

### Trade-offs 고려사항

**Apache Spark on EMR 장점**:
- MLlib을 통한 내장된 협업 필터링 알고리즘 (ALS) 제공
- 대규모 데이터셋에 대한 분산 처리 능력
- 관리형 클러스터로 인프라 관리 부담 최소화
- 다양성, 우연성, 참신성 측면에서 우수한 추천 품질

**Apache Spark on EMR 단점**:
- 초기 클러스터 구성 및 튜닝에 전문 지식 필요
- 실시간 추천보다는 배치 처리에 더 적합
- 콜드 스타트 문제 (신규 사용자/제품) 해결 필요

**Apache HBase 장점**:
- NoSQL 데이터베이스로서 빠른 읽기/쓰기 성능
- 대용량 데이터 저장에 적합

**Apache HBase 단점**:
- 단순 데이터베이스로 머신러닝 기능 부재
- 추천 알고리즘 구현을 위해서는 별도의 ML 프레임워크 필요

## 🔍 주요개념

### 추천 시스템 알고리즘 비교

- **협업 필터링**: 사용자 간 또는 아이템 간 유사성을 기반으로 추천하는 방식으로, 사용자의 과거 행동 패턴과 다른 사용자들의 선호도를 분석하여 예측
- **콘텐츠 기반 필터링**: 아이템의 속성과 특성을 분석하여 사용자의 과거 선호 아이템과 유사한 속성을 가진 제품을 추천하는 방식

### 실전 적용

- **전자상거래 플랫폼**: "이 상품을 구매한 고객들이 함께 본 상품" 추천 기능
- **스트리밍 서비스**: 시청 이력이 비슷한 사용자들의 선호작품 기반 콘텐츠 추천
- **소셜 미디어**: 팔로우 패턴과 상호작용이 유사한 사용자들의 관심사 기반 콘텐츠 큐레이션

## 📝 관련 문제

**Question:** A company wants to boost its sales through a Machine Learning-driven solution. The company has contracted a Machine Learning Specialist to create a recommendation system that will predict products a customer is likely to buy based on his similarity to other customer's product preferences. What action should be taken by the ML Specialist?

**Options:**

- A) Use Apache Spark on Amazon EMR to create a content-based filtering recommendation engine
- B) Use Apache HBase on Amazon EMR to create a collaborative filtering recommendation engine
- C) Use Apache HBase on Amazon EMR to create a content-based filtering recommendation engine
- D) Use Apache Spark on Amazon EMR to create a collaborative filtering recommendation engine

**정답: D) Use Apache Spark on Amazon EMR to create a collaborative filtering recommendation engine**

💡 추가 설명:

- **Option A** - 콘텐츠 기반 필터링은 제품 속성을 기반으로 하므로, 고객 간 유사성을 활용하는 요구사항에 부적합
- **Option B** - HBase는 데이터베이스로 머신러닝 기능이 없어 협업 필터링 알고리즘 구현 불가능
- **Option C** - HBase로는 머신러닝 작업 수행 불가하며, 콘텐츠 기반 필터링도 문제 요구사항에 맞지 않음