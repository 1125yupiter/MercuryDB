---
title: sagemaker-builtin-dataformats
created: 2025-08-16
modified: 2025-08-16
tags:
- service/sagemaker
- data/csv
- data/recordio
- algorithm/builtin
- training/format
aliases: ["sagemaker data format", "builtin algorithm format", "training data format"]
---

# Amazon SageMaker 빌트인 알고리즘 지원 데이터 포맷

## 🎯 핵심 포인트

EC2에서 분류와 회귀 모델을 훈련하던 경우 SageMaker 빌트인 알고리즘에서, **CSV**와 **RecordIO-Protobuf** 포맷을 주로 사용할 수 있다.

## 📝 설명

### SageMaker 빌트인 알고리즘이 다양한 데이터 포맷을 지원하는 이유

SageMaker는 다양한 머신러닝 작업(분류, 회귀, 클러스터링, 이미지 처리 등)을 위한 빌트인 알고리즘을 제공하며, 각 알고리즘의 특성에 맞는 최적화된 데이터 포맷을 지원합니다. 데이터 포맷은 알고리즘의 성능과 처리 효율성에 직접적인 영향을 미치므로, 올바른 포맷 선택이 중요합니다.

### 아키텍처 플로우

```
EC2 Training Data → S3 Bucket → SageMaker Training Job
                                      ↓
                              Content Type 지정
                                      ↓
                            알고리즘별 데이터 파싱
                                      ↓
                               모델 훈련 수행
```

### Trade-offs 고려사항

**CSV 포맷 장점**:
- 가장 널리 사용되는 표준 포맷으로 호환성이 높음
- 대부분의 테이블 형태 데이터 알고리즘에서 지원
- 사람이 읽기 쉽고 디버깅이 용이함
- Pipe Mode 지원으로 빠른 스트리밍 가능

**CSV 포맷 단점**:
- 헤더가 없어야 하고 타겟 변수가 첫 번째 열에 위치해야 함
- 텍스트 기반이라 파일 크기가 상대적으로 큼
- 복잡한 데이터 구조 표현에 제한적

**RecordIO-Protobuf 장점**:
- 바이너리 포맷으로 압축 효율성이 높음
- 스파스 데이터 처리에 최적화
- 대용량 데이터 처리 시 성능 우수

**RecordIO-Protobuf 단점**:
- 사람이 읽기 어려워 디버깅이 복잡함
- 데이터 변환 과정이 필요함
- Python 외 언어에서 변환 작업이 복잡

## 🔍 주요개념

### 주요 데이터 포맷 비교

- **text/csv**: 테이블 형태 데이터용 표준 포맷, 헤더 없이 타겟이 첫 열에 위치
- **application/x-recordio-protobuf**: 4바이트 float 바이너리 표현, 스파스 데이터 최적화
- **image/jpeg, image/png**: 이미지 처리 알고리즘 전용 포맷
- **application/jsonlines**: 텍스트 및 시계열 데이터용 JSON 라인 포맷
- **text/libsvm**: XGBoost 전용 스파스 벡터 포맷

### 실전 적용

- **금융 사기 탐지**: Linear Learner + CSV 포맷으로 실시간 분류
- **고객 세분화**: K-Means + RecordIO-Protobuf로 대용량 클러스터링
- **제품 추천**: Factorization Machines + RecordIO-Protobuf로 스파스 매트릭스 처리

## 📝 관련 문제

**Question:** A Machine Learning Specialist has been using Amazon EC2 for quite some time to train classification and regression models. The Specialist wants to simplify the training job by leveraging Amazon SageMaker's built-in algorithms. However, he is unsure if SageMaker can support the format of his training data. Which data formats can the Specialist use for model training? (Select TWO.)

**Options:**

- A) CSV
- B) HDF5
- C) JPG
- D) TSV
- E) TFRecord

**정답: A) CSV, C) JPG**

💡 추가 설명:

- **HDF5** - SageMaker 빌트인 알고리즘에서 직접 지원하지 않는 포맷
- **TSV** - Tab으로 구분된 포맷이지만 SageMaker는 CSV(콤마 구분)만 지원
- **TFRecord** - TensorFlow 전용 포맷으로 SageMaker 빌트인 알고리즘에서 미지원

---