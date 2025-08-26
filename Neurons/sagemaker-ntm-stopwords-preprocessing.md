---

title: sagemaker-ntm-stopwords-preprocessing
created: 2025-08-26
modified: 2025-08-26
tags:
- service/sagemaker
- algorithm/ntm
- preprocessing/countvectorizer
- problem/stopwords
- technique/text-cleaning
aliases: ["NTM", "Neural Topic Model", "CountVectorizer", "stopwords"]

---

# SageMaker NTM에서 불용어 제거를 통한 태그 품질 개선

## 🎯 핵심 포인트

블로그 태그 추천 모델에서 불용어가 태그로 제안되는 경우, CountVectorizer를 통해 전처리 단계에서 불용어를 완전히 제거하여 정제된 데이터로 NTM을 재훈련할 수 있다.

## 📝 설명

### CountVectorizer가 NTM 전처리에 적합한 이유

CountVectorizer는 텍스트 데이터를 벡터로 변환하는 과정에서 `stop_words` 매개변수를 통해 불용어를 원천 차단할 수 있습니다. 이는 모델이 학습하기 전에 데이터 자체에서 불용어를 완전히 제거하므로, NTM이 불용어를 태그 후보로 학습할 가능성을 원천 봉쇄합니다.

핵심은 **사후 필터링이 아닌 사전 데이터 정제** 접근법입니다. 불용어가 제거된 깨끗한 텍스트 데이터로 모델을 재훈련하면, 애초에 불용어가 존재하지 않는 학습 환경이 구성됩니다.

### 아키텍처 플로우

```
Raw Blog Data (S3) 
    ↓
CountVectorizer 전처리
- stop_words='english' 설정
- 불용어 완전 제거
- 희귀 단어 보존 (min_df, max_df 조정)
    ↓
정제된 텍스트 데이터 (S3)
    ↓
SageMaker NTM 재훈련
    ↓
불용어 없는 태그 추천 모델
```

### Trade-offs 고려사항

**CountVectorizer 장점**:
- 불용어 원천 차단으로 근본적 해결
- 희귀 단어 보존 가능 (min_df, max_df 활용)
- scikit-learn 표준 라이브러리로 안정성 보장
- 기존 NTM 워크플로우 유지 가능

**CountVectorizer 단점**:
- 전체 데이터 재처리 필요
- S3 데이터 교체로 인한 스토리지 비용
- 초기 설정 시 불용어 리스트 커스터마이징 필요

**Amazon Comprehend 장점**:
- 고급 NLP 기능 제공

**Amazon Comprehend 단점**:
- 개체 인식 기능으로 불용어 제거와 직접적 관련 없음
- 추가 API 호출 비용 발생

**PCA 장점**:
- 차원 축소를 통한 계산 효율성

**PCA 단점**:
- 토픽 모델링과 무관한 기법
- 불용어 문제 해결 불가

**Object Detection 장점**:
- 이미지 처리에 특화

**Object Detection 단점**:
- 텍스트 데이터 처리와 완전히 무관
- 문제 상황과 전혀 맞지 않음

## 🔍 주요개념

### 전처리 방식 비교

- **사전 데이터 정제**: CountVectorizer로 불용어를 완전히 제거한 후 모델 학습
- **사후 결과 필터링**: 모델 결과에서 불용어 태그만 제거 (근본적 해결 아님)

### 실전 적용

- **블로그 태그 시스템**: 기술 블로그에서 "the", "and" 같은 불용어가 태그로 나타나는 문제 해결
- **상품 리뷰 분석**: 리뷰에서 의미있는 키워드만 추출하여 상품 특성 파악
- **뉴스 기사 분류**: 기사 내용에서 핵심 토픽만 추출하여 자동 카테고리 분류

## 📝 관련 문제

**Question:** A data scientist is developing a model that selects tags from blog articles using the Amazon SageMaker Neural Topic Model (NTM) algorithm. The raw blog post data is kept in JSON format in an Amazon S3 bucket. During model assessment, the data scientist observed that the model proposes certain stopwords such as "a," "an," and "the" as tags for certain blog postings, as well as a few uncommon words that appear in just particular blog entries. Following many cycles of tag review with the content team, the data scientist observes that the rare terms are uncommon yet viable. Additionally, the data scientist must check that the resulting model's tag suggestions do not include stopwords. What actions should the data scientist take to ensure compliance with these requirements?

**Options:**

- A) Use the Amazon Comprehend entity recognition API operations. Remove the detected words from the blog post data. Replace the blog post data source in the S3 bucket.
- B) Run the SageMaker built-in principal component analysis (PCA) algorithm with the blog post data from the S3 bucket as the data source. Replace the blog post data in the S3 bucket with the results of the training job.
- C) Use the SageMaker built-in Object Detection algorithm instead of the NTM algorithm for the training job to process the blog post data.
- D) Remove the stopwords from the blog post data by using the CountVectorizer function in the scikit-learn library. Replace the blog post data in the S3 bucket with the results of the vectorizer.

**정답: D) CountVectorizer를 통한 불용어 제거**

💡 추가 설명:

- **선택지 A** - Amazon Comprehend는 개체 인식(Named Entity Recognition)에 특화되어 있어 불용어 제거와는 직접적 관련이 없음
- **선택지 B** - PCA는 차원 축소 기법으로 토픽 모델링이나 불용어 제거 목적과 무관함
- **선택지 C** - Object Detection은 이미지 처리 알고리즘으로 텍스트 기반 토픽 모델링과 완전히 다른 영역임
- **핵심 이해**: CountVectorizer는 벡터화 과정에서 stop_words 매개변수를 통해 불용어를 학습 데이터에서 원천 배제하므로, NTM이 불용어를 학습할 기회 자체를 차단함