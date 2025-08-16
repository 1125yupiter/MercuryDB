---
title: sagemaker-custom-container-tensorflow
created: 2025-08-16
modified: 2025-08-16
tags:

  - service/sagemaker
  - technology/docker
  - technology/tensorflow
  - deployment/custom-algorithm
  - method/deep-learning
aliases: ["SageMaker custom container", "SageMaker Docker", "TensorFlow on SageMaker"]
---

# SageMaker에서 TensorFlow 커스텀 알고리즘을 Docker 컨테이너로 배포하는 방법

## 🎯 핵심 포인트

머신러닝 전문가가 TensorFlow 기반의 커스텀 알고리즘을 Amazon SageMaker에서 실행해야 하는 경우, Dockerfile의 `ENTRYPOINT`를 사용하여 훈련 스크립트를 컨테이너의 주요 실행 명령어로 지정해야 합니다.

-----

## 📝 설명

### SageMaker가 커스텀 컨테이너에 적합한 이유

SageMaker는 기본적으로 널리 사용되는 프레임워크(TensorFlow, PyTorch 등)용 컨테이너를 제공하지만, 특수한 요구사항이 있는 경우 커스텀 컨테이너를 사용하여 환경을 완벽하게 제어할 수 있습니다. 예를 들어, **특정 버전의 라이브러리**, **기본 컨테이너에 없는 패키지**, 혹은 **독자적인 알고리즘**을 사용해야 할 때 유용합니다.

커스텀 컨테이너는 SageMaker가 아닌 다른 환경(온프레미스, 다른 클라우드)에서도 동일하게 동작할 수 있어, **개발과 배포 환경의 일관성**을 유지하는 데 도움이 됩니다.

### 커스텀 컨테이너 아키텍처 플로우

```
로컬 머신 (Dockerfile, 훈련 스크립트)
  ↓
Docker Image Build (docker build)
  ↓
Amazon ECR (Docker 이미지 푸시)
  ↓
SageMaker Training Job Start (S3에서 데이터 불러옴)
  ↓
SageMaker가 ECR에서 Docker 이미지 가져오기
  ↓
SageMaker가 컨테이너 실행 (docker run)
  ↓
컨테이너 내부 ENTRYPOINT에 지정된 훈련 스크립트 실행
  ↓
훈련 완료 후 SageMaker가 모델 아티팩트 S3에 저장
```

### Trade-offs 고려사항

**SageMaker 커스텀 컨테이너 장점**:

  - **유연성**: 사용자가 원하는 모든 라이브러리, 의존성, 환경을 구성할 수 있습니다.
  - **이식성**: Docker 컨테이너는 어떤 환경에서든 동일하게 작동하므로, SageMaker 외의 다른 플랫폼으로의 이전이 용이합니다.
  - **환경 제어**: 특정 라이브러리 버전을 고정하여 훈련 재현성을 보장할 수 있습니다.

**SageMaker 커스텀 컨테이너 단점**:

  - **복잡성**: Dockerfile 작성, 이미지 빌드, ECR(Elastic Container Registry) 관리 등 추가적인 작업이 필요합니다.
  - **유지보수**: 의존성 업데이트, 보안 패치 등 컨테이너 이미지의 지속적인 관리가 필요합니다.

**SageMaker 기본 컨테이너 장점**:

  - **편의성**: Dockerfile 없이 훈련 스크립트만 작성하면 되므로, 개발 시간이 단축됩니다.
  - **쉬운 시작**: 초보자나 표준 프레임워크를 사용하는 사용자에게 적합합니다.

**SageMaker 기본 컨테이너 단점**:

  - **제한적인 환경**: 필요한 라이브러리가 기본 컨테이너에 포함되어 있지 않다면 사용할 수 없습니다.
  - **낮은 유연성**: SageMaker가 제공하는 환경에 의존해야 합니다.

-----

## 🔍 주요 개념

### `ENTRYPOINT` vs. `CMD`

  - **`ENTRYPOINT`**: 컨테이너의 **주요 실행 파일**을 지정합니다. `docker run` 명령에 인자가 추가되어도 `ENTRYPOINT`는 항상 실행되며, 추가 인자는 스크립트로 전달됩니다. SageMaker 환경에서 훈련 스크립트를 실행하기 위해 권장되는 방식입니다.
  - **`CMD`**: 컨테이너의 **기본 실행 명령어**를 지정합니다. `docker run` 명령에 다른 인자가 추가되면 `CMD`는 **무시**됩니다. SageMaker가 `train` 인자를 전달하므로 `CMD`를 사용하면 훈련 스크립트가 실행되지 않습니다.

### 실전 적용

  - **신약 개발 모델**: 특정 제약사에서만 사용하는 독점적인 계산 라이브러리가 필요한 경우, 커스텀 컨테이너를 사용해 환경을 구축합니다.
  - **특허 받은 딥러닝 알고리즘**: 공개되지 않은 복잡한 훈련 로직을 실행하기 위해, 소스 코드를 Docker 컨테이너에 담아 보안을 유지하며 훈련합니다.
  - **오래된 라이브러리 호환성**: 최신 프레임워크와 호환되지 않는 구형 라이브러리로 모델을 훈련해야 할 때, 커스텀 컨테이너로 특정 버전의 환경을 구축합니다.

-----

## 📝 관련 문제

**Question:** A Machine Learning Specialist has a custom algorithm bundled in a TensorFlow Docker container. The Specialist needs to configure the container so that it could be run as executable by Amazon SageMaker.
Which course of action should the Specialist take?

**Options:**

  - A) Specify a command in the `CMD` that launches the training program when run.
  - B) Modify the Docker file by adding a `RUN` command that initializes the training script at build time.
  - C) Modify the Docker file by adding the training script as an `ENTRYPOINT`.
  - D) Place the training script inside the `/opt/ml/code`.

**정답: C) Modify the Docker file by adding the training script as an `ENTRYPOINT`.**

💡 추가 설명:

  - **오답 옵션 A** - SageMaker는 `docker run` 명령에 `train` 같은 인자를 추가하여 `CMD`를 무시합니다.
  - **오답 옵션 B** - `RUN` 명령어는 Docker 이미지 빌드 시에만 실행되며, 컨테이너가 훈련 작업을 수행할 때는 실행되지 않습니다.
  - **오답 옵션 D** - `/opt/ml/code`는 훈련 스크립트가 위치해야 하는 규약적인 경로일 뿐, 이 경로에 있다고 해서 자동으로 실행되지는 않습니다. 어떤 스크립트를 실행할지 명시적으로 지정해야 합니다.