# MercuryDB 파일명 포맷 가이드

## 📝 기본 원칙
- **소문자만 사용** (검색 일관성)
- **하이픈(-)으로 단어 구분**
- **태그 기반 명명** (내용과 일치)
- **3-4개 핵심 키워드** (너무 길지 않게)

## 📐 표준 포맷

```
[problem]-[service]-[constraint].md
[problem]-[service]-[technique].md
[service]-[usecase]-[constraint].md
```

## 🏷️ 태그 → 파일명 변환 규칙

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

## 📋 파일명 예시

### ✅ 표준 케이스
```markdown
태그: #problem:classification #service:sagemaker #constraint:real-time
파일명: classification-sagemaker-realtime.md

태그: #problem:anomaly #service:kinesis #service:sagemaker
파일명: anomaly-kinesis-sagemaker.md

태그: #problem:forecasting #service:forecast #industry:retail
파일명: forecast-retail-demand.md
```

### 🔀 특수 케이스

#### 비교 문서
```
compare-kinesis-kafka.md
compare-batch-realtime.md
sagemaker-vs-bedrock.md
```

#### 파이프라인/조합
```
pipeline-s3-glue-sagemaker.md
flow-kinesis-lambda-dynamodb.md
etl-glue-athena-quicksight.md
```

#### 안티패턴/실수
```
antipattern-training-test-leak.md
antipattern-overfitting.md
mistake-not-scaling-features.md
mistake-wrong-metric.md
```

#### 체크리스트/가이드
```
checklist-deployment.md
checklist-data-quality.md
guide-hyperparameter-tuning.md
guide-feature-selection.md
```

#### 규제/컴플라이언스
```
compliance-hipaa-ml.md
compliance-gdpr-preprocessing.md
security-vpc-endpoint.md
```

## ❌ 피해야 할 것

```markdown
❌ RealTime-Fraud-Detection.md     (대문자 혼용)
❌ real_time_fraud_detection.md     (언더스코어)
❌ realtimefrauddetection.md        (구분자 없음)
❌ classification-sagemaker-realtime-lowcost-health-gdpr.md (너무 김)
❌ solution1.md                     (의미 없는 이름)
❌ 20240115-classification.md       (날짜 포함 불필요)
❌ clf-sm-rt.md                     (과도한 축약)
```

## 🔄 중복 처리 전략

### 같은 태그 조합일 때
```markdown
옵션 1: 구체화
classification-sagemaker-realtime.md        (일반)
classification-sagemaker-realtime-fraud.md  (사기 탐지)
classification-sagemaker-realtime-medical.md (의료)

옵션 2: 버전 관리
classification-sagemaker-realtime.md     (기본)
classification-sagemaker-realtime-v2.md  (개선)
classification-sagemaker-realtime-adv.md (고급)
```

## 📊 파일명 결정 프로세스

```
1. 핵심 태그 3-4개 선택
   예: #problem:anomaly, #service:kinesis, #constraint:real-time

2. 우선순위 순 정렬
   problem → service → constraint → technique → industry

3. 축약 규칙 적용
   anomaly, kinesis, realtime

4. 하이픈으로 연결
   anomaly-kinesis-realtime

5. .md 확장자 추가
   anomaly-kinesis-realtime.md
```

## 💡 실전 팁

### 파일명 길이
- **이상적**: 3개 키워드 (예: `classification-sagemaker-imbalanced.md`)
- **최대**: 4개 키워드 (예: `anomaly-kinesis-sagemaker-finance.md`)
- **최소**: 2개 키워드 (예: `sagemaker-hpo.md`)

### 검색 최적화
- 가장 중요한 키워드를 앞에 배치
- 일반적인 용어보다 구체적인 용어 사용
- 동의어보다 일관된 용어 사용

### 일관성 유지
- 한번 정한 축약 규칙 고수
- 팀/프로젝트 전체에서 동일한 규칙 사용
- 규칙 변경 시 기존 파일도 일괄 수정

## 📝 Quick Reference

| 카테고리       | 원본 태그                       | 파일명 축약             |
| ---------- | --------------------------- | ------------------ |
| Problem    | # problem:classification    | classification     |
| Problem    | # problem:anomaly           | anomaly            |
| Service    | # service:sagemaker         | sagemaker          |
| Service    | # service:comprehend        | comprehend         |
| Constraint | # constraint:real-time      | realtime           |
| Constraint | # constraint:cost-sensitive | lowcost            |
| Technique  | # technique:hyperparameter  | hpo                |
| Industry   | # industry:healthcare       | health             |
| Special    | 비교 문서                       | compare- 또는 -vs-   |
| Special    | 파이프라인                       | pipeline- 또는 flow- |
| Special    | 안티패턴                        | antipattern-       |
| Special    | 실수                          | mistake-           |

---

*Version: 1.0 | MercuryDB Filename Format Guide*