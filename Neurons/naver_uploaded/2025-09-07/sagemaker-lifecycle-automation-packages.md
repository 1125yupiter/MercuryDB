---

title: sagemaker-lifecycle-automation-packages
created: 2025-08-24
modified: 2025-08-24
tags:
- service/sagemaker
- automation/lifecycle
- deployment/notebook-instance
- management/package-installation
- configuration/automated-setup
aliases: ["sagemaker-lifecycle", "notebook-automation", "lifecycle-config"]

---

# SageMaker Lifecycle Configuration을 활용한 노트북 인스턴스 패키지 자동화

## 🎯 핵심 포인트

SageMaker 노트북 인스턴스에서 필수 Python 패키지를 자동으로 설치해야 하는 경우, Lifecycle Configuration을 사용하여 인스턴스 생성 및 시작 시점에 자동 설치를 구현할 수 있다.

## 📝 설명

### SageMaker Lifecycle Configuration이 패키지 자동화에 적합한 이유

Amazon SageMaker Lifecycle Configuration은 노트북 인스턴스의 생명주기 동안 특정 시점에 자동으로 스크립트를 실행할 수 있는 네이티브 기능입니다. 이를 통해 데이터 사이언티스트가 매번 수동으로 패키지를 설치할 필요 없이 항상 필요한 환경을 보장할 수 있습니다.

### 아키텍처 플로우

```
노트북 인스턴스 생성/시작
        ↓
Lifecycle Configuration 트리거
        ↓
onCreate/onStart 스크립트 실행
        ↓
필수 패키지 자동 설치
        ↓
사용자 접속 시 즉시 사용 가능
```

### Trade-offs 고려사항

**Lifecycle Configuration 장점**:
- SageMaker의 네이티브 기능으로 완전 통합
- onCreate/onStart 시점 제어 가능
- 인스턴스 재시작 시에도 자동 실행
- 스크립트 기반으로 복잡한 설치 프로세스 구현 가능

**Lifecycle Configuration 단점**:
- 초기 설정이 필요하며 스크립트 작성 필요
- 설치 시간만큼 인스턴스 시작이 지연될 수 있음

**수동 설치 방법의 단점**:
- 매번 수동 작업 필요
- 인스턴스 재시작 시 패키지 유실 가능성
- 일관성 없는 환경 설정

**다른 자동화 방법들의 한계**:
- Systems Manager: SageMaker 환경에 부적합하고 복잡함
- /etc/init 디렉토리: Jupyter 노트북 파일 실행 불가
- conda 매니저만 사용: 자동화되지 않고 수동 개입 필요

## 🔍 주요개념

### 스크립트 실행 시점 비교

- **onCreate 스크립트**: 노트북 인스턴스가 처음 생성될 때 한 번만 실행. 시간이 오래 걸리는 대용량 패키지 설치에 적합
- **onStart 스크립트**: 노트북 인스턴스가 시작될 때마다 실행 (재시작 포함). 환경 변수 설정이나 빠른 설정에 적합

### 실전 적용

- **데이터 사이언스 팀 표준화**: 모든 팀원이 동일한 패키지 환경에서 작업 시작
- **프로젝트별 환경 구성**: 특정 프로젝트에 필요한 전용 라이브러리 자동 설치
- **보안 및 컴플라이언스**: 승인된 패키지만 자동으로 설치하여 환경 통제

## 📝 관련 문제

**Question:** A data scientist conducts data exploration and analysis using an Amazon SageMaker notebook instance. This involves installing some Python packages on the notebook instance that are not natively accessible on Amazon SageMaker. How can a machine learning professional guarantee that the data scientist's essential packages are automatically accessible on the notebook instance?

**Options:**

- A) Install AWS Systems Manager Agent on the underlying Amazon EC2 instance and use Systems Manager Automation to execute the package installation commands
- B) Create a Jupyter notebook file (.ipynb) with cells containing the package installation commands to execute and place the file under the /etc/init directory of each Amazon SageMaker notebook instance
- C) Use the conda package manager from within the Jupyter notebook console to apply the necessary conda packages to the default kernel of the notebook
- D) Create an Amazon SageMaker lifecycle configuration with package installation commands and assign the lifecycle configuration to the notebook instance

**정답: D) Amazon SageMaker lifecycle configuration**

💡 추가 설명:

- **A) Systems Manager Automation** - SageMaker 노트북 인스턴스의 일반적인 관리 방법이 아니며, 복잡하고 SageMaker 네이티브 기능을 활용하지 않음
- **B) /etc/init 디렉토리** - 시스템 초기화 스크립트 디렉토리로 .ipynb 파일은 여기서 실행되지 않으며 기술적으로 불가능
- **C) conda 패키지 매니저** - 수동으로 매번 설치해야 하며 "자동으로" 접근 가능하도록 보장하지 못하고, 인스턴스 재시작 시 패키지 유실 가능