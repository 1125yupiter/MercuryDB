---

title: sagemaker-tensorflow-script-mode
created: 2025-08-25
modified: 2025-08-25
tags:
- service/sagemaker
- framework/tensorflow
- deployment/script-mode
- storage/s3
- constraint/minimal-development
aliases: ["sagemaker-tf", "script-mode", "tfrecord-training"]

---

# Amazon SageMaker TensorFlow Script Mode를 활용한 효율적인 ML 모델 훈련

## 🎯 핵심 포인트

기존 TensorFlow 스크립트와 TFRecord 데이터가 있는 경우 Amazon SageMaker Script Mode에서, 코드 수정 없이 S3 버킷 연결만으로 최소한의 개발 시간으로 클라우드 훈련을 구현할 수 있다.

## 📝 설명

### SageMaker Script Mode가 기존 TensorFlow 워크플로우에 적합한 이유

Amazon SageMaker는 TensorFlow를 네이티브로 지원하며, Script Mode를 통해 기존 훈련 스크립트를 그대로 활용할 수 있습니다. TensorFlow Training Container가 사전 구성되어 있어 TFRecord 포맷을 포함한 모든 TensorFlow 기능을 지원합니다.

핵심은 **코드 변경 없이 데이터 위치만 로컬에서 S3로 변경**하는 것입니다. SageMaker는 S3에서 훈련 인스턴스로 데이터를 자동 다운로드하여 기존 `input_path`에 전달해줍니다.

### 아키텍처 플로우

```
기존 TensorFlow 스크립트 (train.py)
        ↓ (변경 없음)
TFRecord 데이터 → S3 버킷 업로드
        ↓
SageMaker 훈련 작업 생성
        ↓
관리형 TensorFlow Container
        ↓
자동 데이터 다운로드 & 훈련 실행
```

### Trade-offs 고려사항

**SageMaker Script Mode + S3 장점**:
- 기존 `train.py` 스크립트 완전 재사용
- TFRecord 포맷 그대로 사용 가능
- 관리형 TensorFlow 환경 제공
- 확장 가능한 컴퓨팅 리소스
- 최소한의 개발 시간 소요

**SageMaker Script Mode + S3 단점**:
- S3 업로드 시간 필요
- 네트워크 대역폭에 따른 데이터 전송 지연
- AWS 비용 발생

**로컬 경로 사용 장점**:
- 데이터 전송 시간 없음

**로컬 경로 사용 단점**:
- SageMaker 클라우드 환경에서 로컬 데이터 접근 불가능
- 실행 자체가 불가능

**커스텀 포맷 변환 장점**:
- 특정 최적화 가능

**커스텀 포맷 변환 단점**:
- 추가 개발 시간 대폭 증가
- 데이터 파이프라인 복잡성 증가
- 기존 코드 재작성 필요

## 🔍 주요개념

### 컨테이너 지원 방식 비교

- **관리형 컨테이너**: AWS가 사전 구성한 TensorFlow, PyTorch 등의 컨테이너 사용
- **커스텀 컨테이너**: Docker 이미지를 직접 구축하여 특별한 프레임워크나 환경 구성

### 실전 적용

- 기존 TensorFlow 모델을 클라우드로 마이그레이션할 때
- TFRecord 데이터셋을 보유한 팀의 SageMaker 도입 시
- 최소한의 코드 변경으로 스케일아웃이 필요한 경우

## 📝 관련 문제

**Question:** A machine learning specialist is developing a data storage solution for Amazon SageMaker. There is already a TensorFlow-based model developed as a train.py script that makes use of static training data saved as TFRecords. Which approach of supplying training data to Amazon SageMaker would satisfy business needs with the LEAST amount of development time?

**Options:**

- A) Use Amazon SageMaker script mode and use train.py unchanged. Point the Amazon SageMaker training invocation to the local path of the data without reformatting the training data.
- B) Use Amazon SageMaker script mode and use train.py unchanged. Put the TFRecord data into an Amazon S3 bucket. Point the Amazon SageMaker training invocation to the S3 bucket without reformatting the training data.
- C) Rewrite the train.py script to add a section that converts TFRecords to protobuf and ingests the protobuf data instead of TFRecords.
- D) Prepare the data in the format accepted by Amazon SageMaker. Use AWS Glue or AWS Lambda to reformat and store the data in an Amazon S3 bucket.

**정답: B) Use Amazon SageMaker script mode and use train.py unchanged. Put the TFRecord data into an Amazon S3 bucket. Point the Amazon SageMaker training invocation to the S3 bucket without reformatting the training data.**

💡 추가 설명:

- **A 선택지** - SageMaker 클라우드 환경에서 로컬 경로에 직접 접근 불가능
- **C 선택지** - train.py 스크립트 재작성으로 개발 시간 대폭 증가
- **D 선택지** - AWS Glue/Lambda를 통한 데이터 재포맷 작업으로 추가 개발 시간 필요