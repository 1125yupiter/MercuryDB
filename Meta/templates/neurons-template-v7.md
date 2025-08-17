이 대화 내용을 바탕으로 다음 포멧에 맞춰서 아티팩트에 작성해줘. 대화내용을 충분히 담고 정리하기 위해 '설명'부분의 포멧은 자유롭게 작성해도 좋아

---

title: 
created: 오늘날짜 
modified: 오늘날짜 
tags:[핵심포인트 기반으로 태그 선정]
- category/value
- category/value
- category/value
- category/value
- category/value aliases: ["별칭1", "별칭2", "줄임말" 영어로만]

---

# [주제 제목 - 핵심 서비스/기술과 사용 사례를 포함]

## 🎯 핵심 포인트

[핵심 내용을 "~한 경우 ~에서, ~할 수 있다" 형식으로 한 문장으로 작성]

## 📝 설명 [양식 수정이 필요한경우 내용에 맞춰서 수정 가능]

### [서비스/기술]가 [사용 사례]에 적합한 이유

[해당 서비스/기술의 핵심 특징과 작동 방식을 설명. 기술적 세부사항과 실제 적용 시 고려사항을 포함]

### 아키텍처 플로우

```
[시스템 플로우를 시각적으로 표현]
[단계별 프로세스를 화살표로 연결]
```

### Trade-offs 고려사항

**[주요 서비스] 장점**:

- [핵심 장점들을 나열]

**[주요 서비스] 단점**:

- [실제 운영 시 고려할 단점들]

**[대안 서비스] 장점**:

- [비교 대상의 장점]

**[대안 서비스] 단점**:

- [왜 적합하지 않은지 명확히]

## 🔍 주요개념

### [핵심 개념 비교]

- **[개념 A]**: [정의와 특징]
- **[개념 B]**: [정의와 특징]

### 실전 적용

- [구체적인 비즈니스 시나리오 1]
- [구체적인 비즈니스 시나리오 2]
- [구체적인 비즈니스 시나리오 3]

## 📝 관련 문제

**Question:** [실제 시나리오 기반 문제, 영어로]

**Options:**

- A) [선택지 1]
- B) [선택지 2]
- C) [선택지 3]
- D) [선택지 4]
- E) [선택지 5]

**정답: [정답 옵션]) [정답 서비스/솔루션]**

💡 추가 설명:

- **[오답 옵션 A]** - [왜 부적합한지 핵심 이유]
- **[오답 옵션 B]** - [왜 부적합한지 핵심 이유]
- **[오답 옵션 C]** - [왜 부적합한지 핵심 이유]
- **[오답 옵션 D]** - [왜 부적합한지 핵심 이유]

---

# 템플릿 작성 가이드 [작성시 참고만 할 것]

## 작성 원칙

1. **핵심 포인트**: 반드시 "~한 경우 ~에서, ~할 수 있다" 형식으로 작성
2. **제목 표현**: "최적" 대신 "적합한", "효과적인" 등 객관적 표현 사용
3. **추가 설명**: 문제 섹션 아래 주석으로 배치, 불릿 포인트(•) 사용
4. **태그**: 글의 핵심 내용을 반영한 5개 선정

## 프론트매터 작성 가이드

- **title**: 메인태그로 태그-태그-태그-태그.md 형식
- **created**: 작성 날짜 (YYYY-MM-DD 형식)
- **modified**: 수정 날짜 (YYYY-MM-DD 형식)
- **tags**: 계층 구조로 작성 (category/value 형태)
- **aliases**: 검색 시 사용할 별칭들 (여러 줄로 작성)

## 태그 선정 기준

- 구체적인 서비스명 (service/sagemaker-endpoints)
- 핵심 제약사항 (constraint/real-time)
- 배포 특성 (deployment/always-on)
- 성능 특성 (latency/milliseconds)
- 구체적 사용 사례 (usecase/customer-churn)

## 주의사항

- 지식저장소 목적에 맞게 객관적이고 중립적인 표현 사용
- 비교 대상 서비스들의 특징을 주석에 간결하게 정리
- 실무 적용 가능한 구체적 시나리오 포함
- Trade-offs를 명확히 제시하여 의사결정 지원

# title 포맷 가이드[작성시 참고만 할 것]

## 📝 기본 원칙
- **소문자만 사용** (검색 일관성)
- **하이픈(-)으로 단어 구분**
- **태그 기반 명명** (내용과 일치)
- **3-4개 핵심 키워드** (너무 길지 않게)

## 📐 표준 포맷

```
[problem]-[service]-[constraint]
[problem]-[service]-[technique]
[service]-[usecase]-[constraint]
```

## 🏷️ 태그 → title 변환 규칙

### Problem 태그 (첫 번째 우선순위)
```
#problem:classification    → classification
#problem:regression       → regression
#problem:clustering       → clustering
#problem:anomaly         → anomaly
#problem:forecasting     → forecast
#problem:recommendation  → recommendation
```

### Service 태그 (두 번째 우선순위)
```
#service:sagemaker       → sagemaker
#service:comprehend      → comprehend
#service:forecast        → forecast
#service:personalize     → personalize
#service:textract        → textract
#service:rekognition     → rekognition
#service:glue           → glue
#service:kinesis        → kinesis
#service:lambda         → lambda
#service:s3             → s3
#service:dynamodb       → dynamodb
#service:athena         → athena
```

### Constraint 태그 (세 번째 우선순위)
```
#constraint:real-time         → realtime
#constraint:batch            → batch
#constraint:near-real-time   → nearrealtime
#constraint:cost-sensitive   → lowcost
#constraint:high-volume      → highvolume
#constraint:low-latency      → lowlatency
#constraint:imbalanced-data  → imbalanced
```

### Technique/Method 태그 (대체 가능)
```
#technique:ensemble          → ensemble
#technique:hyperparameter    → hpo
#technique:class-balancing   → balancing
#technique:feature-engineering → featureeng
#method:deep-learning        → deeplearning
#method:supervised           → supervised
#method:unsupervised        → unsupervised
#method:reinforcement       → reinforcement
```

### Industry 태그 (선택적, 마지막)
```
#industry:healthcare     → health
#industry:finance       → finance
#industry:retail        → retail
#industry:manufacturing → manufact
#industry:media         → media
```

### Algorithm 태그 (선택적)
```
#algorithm:xgboost      → xgboost
#algorithm:randomforest → rf
#algorithm:neural-net   → nn
#algorithm:kmeans       → kmeans
#algorithm:deepar       → deepar
```

---

*Version: 1.2 | MercuryDB Filename Format Guide*