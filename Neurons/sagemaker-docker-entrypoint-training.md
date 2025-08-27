---

title: sagemaker-docker-entrypoint-training
created: 2025-08-27
modified: 2025-08-27
tags:
- service/sagemaker
- deployment/custom-container
- technique/docker-packaging
- constraint/training-pipeline
- method/entrypoint-specification
aliases: ["sagemaker-docker", "custom-training", "entrypoint"]

---

# SageMaker 커스텀 Docker 훈련 컨테이너에서 ENTRYPOINT를 통한 훈련 프로그램 지정

## 🎯 핵심 포인트

커스텀 훈련 알고리즘을 SageMaker에서 실행하는 경우 Docker 컨테이너의 ENTRYPOINT에 훈련 프로그램을 지정하면, SageMaker가 자동으로 필요한 인수를 전달하여 훈련을 시작할 수 있다.

## 📝 설명

### SageMaker가 커스텀 Docker 컨테이너를 실행하는 방식

SageMaker는 훈련 작업을 실행할 때 Docker 컨테이너에 명령줄 인수를 자동으로 전달합니다. 이 인수들에는 입력 데이터 위치(`/opt/ml/input/data/`), 모델 저장 위치(`/opt/ml/model/`), 하이퍼파라미터 등이 포함됩니다. ENTRYPOINT에 훈련 프로그램을 지정하면 SageMaker가 이를 "진입점"으로 인식하고 모든 필요한 정보를 자동으로 전달합니다.

### 아키텍처 플로우

```
Docker 컨테이너 빌드 → ENTRYPOINT 지정 → SageMaker 등록
    ↓
SageMaker 훈련 작업 시작 → 컨테이너 실행
    ↓
ENTRYPOINT 프로그램 실행 + 자동 인수 전달
    ↓
훈련 프로그램이 데이터 로드 → 모델 훈련 → 모델 저장
```

### 올바른 Dockerfile 구성

```dockerfile
FROM python:3.8

# 의존성 설치
COPY requirements.txt .
RUN pip install -r requirements.txt

# 훈련 스크립트를 컨테이너에 복사
COPY train.py /opt/ml/code/train.py
COPY model.py /opt/ml/code/model.py

# 작업 디렉터리 설정
WORKDIR /opt/ml/code

# 핵심: 훈련 프로그램을 ENTRYPOINT로 지정
ENTRYPOINT ["python", "train.py"]
```

### Trade-offs 고려사항

**ENTRYPOINT 사용 장점**:
- SageMaker가 자동으로 표준 인수들을 전달
- 일관된 실행 환경 보장
- 디버깅 및 로깅이 용이

**ENTRYPOINT 사용 단점**:
- 컨테이너가 SageMaker 전용으로 제한됨
- 로컬 테스트 시 별도 스크립트 필요

**CMD 사용의 문제점**:
- SageMaker 인수가 CMD를 완전히 덮어쓸 수 있음
- 예측 불가능한 실행 결과 발생 가능

**server argument 사용 오류**:
- 추론(inference) 시에만 사용되는 개념
- 훈련 과정에서는 부적절

## 🔍 주요개념

### Docker 실행 방식 비교

- **ENTRYPOINT**: 컨테이너 실행 시 고정된 명령어, 인수는 추가로 전달
- **CMD**: 기본 명령어지만 런타임에 완전히 교체 가능

### SageMaker 표준 디렉터리 구조

```
/opt/ml/
├── input/
│   ├── config/
│   │   ├── hyperparameters.json
│   │   └── resourceconfig.json
│   └── data/
│       └── training/
├── model/          # 훈련된 모델 저장 위치
├── code/           # 훈련 코드 위치
└── output/         # 로그 및 기타 출력
```

### 실전 적용

- **Python 기반 ML 모델**: `ENTRYPOINT ["python", "train.py"]`
- **R 기반 통계 모델**: `ENTRYPOINT ["Rscript", "train.R"]`
- **Java 기반 분산 처리**: `ENTRYPOINT ["java", "-jar", "training.jar"]`

## 📝 관련 문제

**Question:** A machine learning specialist wants to bring a custom training algorithm to Amazon SageMaker. The ML specialist implements the algorithm in a Docker container that is supported by SageMaker. How should the ML specialist package the Docker container so that SageMaker can launch the training correctly?

**Options:**

- A) Specify the server argument in the ENTRYPOINT instruction in the Dockerfile
- B) Specify the training program in the ENTRYPOINT instruction in the Dockerfile
- C) Include the path to the training data in the docker build command when packaging the container
- D) Use a COPY instruction in the Dockerfile to copy the training program to the /opt/ml/train directory

**정답: B) Specify the training program in the ENTRYPOINT instruction in the Dockerfile**

💡 추가 설명:

- **Option A** - server argument는 SageMaker 추론(inference) 시에만 사용되며, 훈련 과정에서는 부적절
- **Option C** - 훈련 데이터 경로는 빌드 타임이 아닌 런타임에 SageMaker가 자동으로 마운트하므로 불필요
- **Option D** - `/opt/ml/train`은 표준 SageMaker 경로가 아니며, 올바른 코드 위치는 `/opt/ml/code`