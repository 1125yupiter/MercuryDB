---

title: sagemaker-data-format-supervised-unsupervised
created: 2025-08-28
modified: 2025-08-28
tags:
- service/sagemaker
- data/csv-format
- learning/supervised-unsupervised
- parameter/label-size
- parameter/header-config
aliases: ["ml-data-format", "sagemaker-csv", "label-size-config"]

---

# AWS SageMaker 머신러닝 데이터 포맷 설정

## 🎯 핵심 포인트

AWS SageMaker에서 supervised learning의 경우 `label_size=1` (기본값)을, unsupervised learning의 경우 `label_size=0`을 명시해야 하며, 헤더가 없는 것이 기본값으로 헤더가 있을 때만 `header=true`를 명시한다.

## 📝 설명

### SageMaker CSV 데이터 포맷의 핵심 원리

AWS SageMaker는 머신러닝 워크플로우의 효율성을 위해 특정 기본값들을 설정하고 있습니다. 이는 실제 ML 실무에서 가장 일반적으로 사용되는 패턴을 반영한 것입니다.

**실무에서의 일반적인 패턴:**
- Supervised learning이 unsupervised learning보다 훨씬 많이 사용됨
- ML용 CSV는 성능과 호환성을 위해 헤더 없이 생성하는 것이 표준

### 데이터 처리 플로우

```
CSV 데이터 생성 → Content-Type 설정 → SageMaker 학습
     ↓                    ↓                  ↓
헤더 없는 순수 데이터   기본값 적용      모델 훈련 수행
label 컬럼 포함      (label_size=1,      
                    header=false)
```

### Content-Type 파라미터 설정 규칙

**기본값 (명시 불필요):**
- `label_size=1` - Supervised learning 가정
- `header=false` - 헤더 없음 가정

**명시 필요한 경우:**
- `label_size=0` - Unsupervised learning
- `label_size=N` (N>1) - Multi-label 또는 multi-target
- `header=true` - 헤더가 있는 경우

### Trade-offs 고려사항

**헤더 없는 CSV 장점**:
- 파싱 성능 향상
- 메모리 효율성 증가
- 자동화 스크립트 단순화
- ML 프레임워크 호환성 증대

**헤더 없는 CSV 단점**:
- 데이터 분석 시 가독성 저하
- 컬럼 순서 의존성 증가
- 디버깅 시 컬럼 식별 어려움

**헤더 있는 CSV 장점**:
- 데이터 분석 및 EDA 용이성
- 컬럼 의미 파악 가능
- 데이터 검증 편의성

**헤더 있는 CSV 단점**:
- 추가 파싱 오버헤드
- 데이터 타입 불일치 가능성

## 🔍 주요개념

### 학습 방식별 label_size 설정

- **Supervised Learning**: `label_size=1` (기본값, 생략 가능)
- **Unsupervised Learning**: `label_size=0` (반드시 명시)
- **Multi-label Classification**: `label_size=N` (N>1, 반드시 명시)
- **Multi-target Regression**: `label_size=N` (N>1, 반드시 명시)

### 실전 적용

- **이미지 분류 (불량품 검출)**: `text/csv` (supervised, 단일 레이블)
- **로봇 행동 패턴 학습**: `text/csv;label_size=0` (unsupervised)
- **다중 레이블 분류**: `text/csv;label_size=3;header=true`
- **데이터 분석용**: `text/csv;header=true`

## 📝 관련 문제

**Question:** You work as a machine learning specialist for a manufacturing plant where you are attempting to use supervised learning to train assembly line image recognition to categorize malformed parts. You have engineered your data and produced a CSV file. Which input data channel specifications are correct? (Select TWO)

**Options:**

- A) Metadata Content-Type is identified as text/csv
- B) Metadata Content-Type is identified as text/csv;label_size=0
- C) Target value should be in the first column with no header
- D) Target value should be in the last column with no header
- E) Target value should be in the last column with a header
- F) Target value should be in the first column with a header

**정답: A, C**

💡 추가 설명:

- **Option B** - `label_size=0`은 unsupervised learning용이므로 supervised learning에서는 부적절
- **Option D, E** - SageMaker는 일반적으로 target을 첫 번째 컬럼에 배치하는 것을 선호
- **Option F** - A에서 header 파라미터가 없으므로 기본값(header=false) 적용, 따라서 헤더 없어야 함

**Question:** You work as a machine learning specialist for a robotics manufacturer where you are attempting to use unsupervised learning to train your robots to perform their prescribed tasks. Which input data channel specification is correct?

**Options:**

- A) Metadata Content-Type is identified as text/csv
- B) Metadata Content-Type is identified as application/x-recordio-protobuf;boundary=1
- C) Metadata Content-Type is identified as application/x-recordio-protobuf;label_size=1
- D) Metadata Content-Type is identified as text/csv;label_size=0

**정답: D**

💡 추가 설명:

- **Option A** - 기본값 label_size=1이 적용되어 supervised learning으로 인식됨
- **Option B, C** - RecordIO format은 다른 용도이며 unsupervised learning 설정이 아님