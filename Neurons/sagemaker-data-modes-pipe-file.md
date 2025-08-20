---

title: sagemaker-data-modes-pipe-file
created: 2025-08-20
modified: 2025-08-20
tags:
- service/sagemaker
- data/input-modes
- constraint/s3-only
- performance/streaming
- architecture/pipe-file
aliases: ["sagemaker-pipe", "data-modes", "streaming"]

---

# Amazon SageMaker 데이터 입력 모드와 지원 데이터 소스

## 🎯 핵심 포인트

대용량 데이터를 스트리밍 방식으로 처리하는 경우 Pipe 모드에서 S3만 지원되며, 다양한 데이터 소스를 활용하는 경우 File 모드를 사용할 수 있다.

## 📝 설명

### SageMaker가 데이터 입력에 적합한 이유

Amazon SageMaker는 머신러닝 모델 학습 시 다양한 데이터 소스와 입력 방식을 지원하여 유연한 데이터 처리가 가능합니다. 특히 데이터 크기와 처리 방식에 따라 최적화된 입력 모드를 선택할 수 있어, 성능과 비용 효율성을 모두 확보할 수 있습니다.

### 아키텍처 플로우

```
데이터 소스 선택
    ↓
입력 모드 결정
    ↓
┌─ Pipe 모드 ────→ S3 스트리밍 ────→ 실시간 처리
│
├─ File 모드 ────→ 로컬 다운로드 ──→ 파일 시스템 접근
│
└─ FastFile 모드 → S3 On-Demand ──→ 빠른 시작
    ↓
학습 컨테이너에서 데이터 처리
```

### Trade-offs 고려사항

**Pipe 모드 장점**:
- 대용량 데이터셋을 메모리 효율적으로 스트리밍 처리
- 디스크 공간 절약 (데이터를 로컬에 저장하지 않음)
- 실시간으로 데이터를 읽어 처리 시작 시간 단축

**Pipe 모드 단점**:
- S3에만 제한적 지원 (다른 데이터 소스 직접 연결 불가)
- 순차 접근만 가능 (랜덤 액세스 불가)
- 재시작 시 처음부터 다시 스트리밍

**File 모드 장점**:
- 다양한 데이터 소스 지원 (S3, EFS, FSx, 외부 소스)
- 랜덤 액세스 가능
- 디버깅과 데이터 검증이 용이

**File 모드 단점**:
- 학습 시작 전 모든 데이터 다운로드 필요
- 로컬 디스크 공간 요구사항 증가
- 대용량 데이터셋의 경우 초기 다운로드 시간 소요

## 🔍 주요개념

### 데이터 입력 모드 비교

- **Pipe 모드**: S3에서 실시간 스트리밍, 메모리 효율적, 대용량 데이터 처리에 최적
- **File 모드**: 로컬 다운로드 후 파일 시스템 접근, 다양한 소스 지원, 랜덤 액세스 가능
- **FastFile 모드**: S3 전용이지만 필요한 데이터만 on-demand 다운로드

### 실전 적용

- **대용량 이미지 분류**: Pipe 모드로 S3의 수백GB 이미지 데이터를 스트리밍 처리
- **하이브리드 환경**: File 모드로 온프레미스 데이터베이스와 클라우드 데이터 통합 학습
- **빠른 프로토타이핑**: FastFile 모드로 S3 데이터를 이용한 신속한 모델 개발

## 📝 관련 문제

**Question:** Your company needs to train a machine learning model using SageMaker with data stored across multiple sources including Amazon S3, Google Cloud Storage, and an on-premises database. The training dataset is 50GB and you need random access to different parts of the data during training. Which data input mode should you choose?

**Options:**

- A) Use Pipe mode with S3 only and migrate all data to S3 first
- B) Use File mode and create custom data loaders for external sources
- C) Use FastFile mode for better performance with mixed data sources
- D) Use Pipe mode with custom containers to access external data sources
- E) Split the data by source and use different input modes simultaneously

**정답: B) Use File mode and create custom data loaders for external sources**

💡 추가 설명:

- **Option A** - Pipe 모드는 S3만 지원하며 랜덤 액세스가 불가능하여 요구사항에 부적합
- **Option C** - FastFile 모드는 S3 전용이므로 다른 클라우드나 온프레미스 데이터 접근 불가
- **Option D** - Pipe 모드는 기본적으로 S3 스트리밍 전용이며 외부 소스 직접 연결 지원하지 않음
- **Option E** - 동시에 여러 입력 모드 사용은 지원되지 않으며 복잡성만 증가