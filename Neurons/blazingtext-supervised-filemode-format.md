---

title: blazingtext-supervised-filemode-format
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker-blazingtext
- method/supervised-learning
- constraint/file-mode
- technique/text-classification
- format/training-data
aliases: ["BlazingText", "supervised training", "file format"]

---

# BlazingText 지도학습 파일 모드에서 적절한 훈련 파일 형식

## 🎯 핵심 포인트

BlazingText 지도학습을 파일 모드로 훈련하는 경우 SageMaker에서, `__label__[숫자]` 형식의 레이블과 공백으로 분리된 모든 토큰을 포함한 형식으로 훈련 파일을 구성할 수 있다.

## 📝 설명

### BlazingText가 텍스트 분류에 적합한 이유

BlazingText는 Facebook의 FastText 알고리즘을 기반으로 한 고성능 텍스트 분류 및 단어 임베딩 알고리즘입니다. 지도학습 모드에서는 레이블이 있는 텍스트 데이터를 사용하여 텍스트 분류 모델을 빠르게 훈련할 수 있습니다. 파일 모드에서는 S3에 저장된 훈련 데이터를 컨테이너로 다운로드하여 사용하며, 특정 형식 요구사항을 반드시 준수해야 합니다.

### 아키텍처 플로우

```
S3 Training Data → SageMaker Training Job → BlazingText Algorithm → Trained Model
     ↓                        ↓                      ↓                   ↓
파일 형식 검증 → 데이터 파싱 → 토큰화 및 학습 → 모델 아티팩트 생성
```

### Trade-offs 고려사항

**BlazingText 파일 모드 장점**:
- S3에서 대용량 데이터셋을 효율적으로 처리
- 빠른 훈련 속도와 낮은 메모리 사용량
- 간단한 텍스트 분류 작업에 최적화

**BlazingText 파일 모드 단점**:
- 엄격한 파일 형식 요구사항
- 복잡한 NLP 작업에는 제한적
- 전처리 과정에서 토큰 분리 작업 필수

**파이프 모드 장점**:
- 실시간 데이터 스트리밍 가능
- 메모리 효율성 향상

**파이프 모드 단점**:
- 구현 복잡성 증가
- 디버깅 어려움

## 🔍 주요개념

### 파일 형식 요구사항

- **레이블 형식**: `__label__[숫자]` - 양쪽에 이중 언더스코어 필수
- **토큰 분리**: 모든 단어와 구두점이 공백으로 분리되어야 함
- **한 줄당 하나의 예시**: 각 훈련 샘플은 개별 라인에 위치

### 실전 적용

- **감성 분석**: `__label__1 this movie is great .`
- **스팸 분류**: `__label__0 urgent click here now !`
- **카테고리 분류**: `__label__3 machine learning tutorial python`

## 📝 관련 문제

**Question:** You are training SageMaker's supervised BlazingText using file mode. Which is an example of a properly formatted line within the training file?

**Options:**

- A) `__label__4 linux ready for prime time, intel says.`
- B) `__label__4 Linux ready for prime time, Intel says.`
- C) `__label4 linux ready for prime time, intel says.`
- D) `__label__4 linux ready for prime time , intel says .`

**정답: D) `__label__4 linux ready for prime time , intel says .`**

💡 추가 설명:

- **Option A** - 구두점(쉼표, 마침표)이 단어와 붙어있어 토큰 분리 규칙 위반
- **Option B** - 구두점 분리 문제와 더불어 대소문자는 형식에 직접적 영향 없음
- **Option C** - `__label4`로 이중 언더스코어 누락, 구두점 분리 문제
- **정답 D** - `__label__` 접두사 올바름, 모든 구두점이 공백으로 적절히 분리됨