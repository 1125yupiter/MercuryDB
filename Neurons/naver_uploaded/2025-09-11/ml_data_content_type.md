---

title: ml-data-content-type-parameters
created: 2025-08-28
modified: 2025-08-28
tags:
- service/sagemaker
- data/csv
- metadata/content-type
- learning/supervised-unsupervised
- format/parameters
aliases: ["content-type", "csv-params", "ml-metadata"]

---

# AWS SageMaker Content-Type 파라미터 설정

## 🎯 핵심 포인트

머신러닝 데이터를 AWS SageMaker에 입력할 경우 Content-Type 파라미터에서 학습 유형(지도/비지도)과 데이터 형식(헤더 유무)을 정확히 명시해야 올바른 데이터 처리가 가능하다.

## 📝 설명

### Content-Type 파라미터가 머신러닝 데이터 처리에 중요한 이유

AWS SageMaker는 입력 데이터의 형식과 구조를 정확히 파악하기 위해 Content-Type 메타데이터를 사용합니다. 잘못된 설정은 모델 학습 실패나 부정확한 결과를 초래할 수 있습니다.

### 주요 파라미터 구성

```
Content-Type: text/csv;label_size=[숫자];has_header=[true/false]
```

**Label Size 설정**:
- `label_size=0`: 비지도 학습 (클러스터링, 차원축소 등)
- `label_size=1`: 지도 학습 (분류, 회귀 등)
- `label_size=n`: 멀티라벨/멀티클래스 문제

**Header 설정**:
- `has_header=true`: 첫 번째 행이 컬럼명
- `has_header=false`: 첫 번째 행부터 데이터

### 실제 데이터 형태에 따른 설정

**헤더가 있는 CSV (has_header=true)**:
```csv
age,income,education,purchased
25,50000,college,1
30,75000,graduate,0
```

**헤더가 없는 CSV (has_header=false)**:
```csv
25,50000,1,0
30,75000,0,1
35,60000,1,0
```

### Trade-offs 고려사항

**CSV 형식 장점**:
- 가독성이 높아 디버깅 용이
- 다양한 도구에서 지원
- 헤더를 통한 컬럼 의미 파악 가능

**CSV 형식 단점**:
- 대용량 데이터 처리 시 성능 저하
- 텍스트 파싱 오버헤드
- 복잡한 데이터 구조 표현 제한

**Protocol Buffer 형식 장점**:
- 바이너리 형식으로 성능 우수
- 스키마 검증 가능
- 압축률 높음

**Protocol Buffer 형식 단점**:
- 가독성 낮음
- 디버깅 어려움
- 스키마 정의 필요

## 🔍 주요개념

### Content-Type 파라미터 비교

- **text/csv**: 일반적인 CSV 형식, 사람이 읽기 쉬움
- **application/x-recordio-protobuf**: 바이너리 형식, 고성능 처리
- **application/x-parquet**: 컬럼형 저장, 분석 최적화
- **application/jsonlines**: JSON 라인 형식, 반구조화 데이터

### 실전 적용 시나리오

- **고객 이탈 예측 (지도학습)**: `text/csv;label_size=1;has_header=true`
- **고객 세분화 (비지도학습)**: `text/csv;label_size=0;has_header=true`
- **실시간 센서 데이터**: `text/csv;label_size=0;has_header=false`

## 📝 관련 문제

**Question:** You work as a machine learning specialist for a robotics manufacturer where you are attempting to use unsupervised learning to train your robots to perform their prescribed tasks. You have engineered your data and produced a CSV file and placed it on S3. Which of the following input data channel specifications are correct for your data?

**Options:**

- A) Metadata Content-Type is identified as text/csv
- B) Metadata Content-Type is identified as application/x-recordio-protobuf;boundary=1
- C) Metadata Content-Type is identified as application/x-recordio-protobuf;label_size=1
- D) Metadata Content-Type is identified as text/csv;label_size=0

**정답: D) text/csv;label_size=0**

💡 추가 설명:

- **A) text/csv** - 기본 CSV 형식이지만 비지도학습을 위한 라벨 정보가 누락
- **B) boundary=1** - Protocol Buffer에서 boundary 파라미터는 부적절함
- **C) label_size=1** - 지도학습용 설정으로 비지도학습에 부적합
- **D) 정답** - CSV 형식이면서 label_size=0으로 비지도학습에 적합

---