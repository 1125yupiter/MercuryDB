---

title: sagemaker-notebook-security-upgrade
created: 2025-08-23
modified: 2025-08-23
tags:
- service/sagemaker
- constraint/security
- deployment/lifecycle
- maintenance/upgrade
- usecase/notebook-management
aliases: ["sagemaker-upgrade", "notebook-security", "lifecycle-config"]

---

# SageMaker 노트북 인스턴스 보안 및 소프트웨어 업그레이드

## 🎯 핵심 포인트

장기간 운영 중인 SageMaker 노트북 인스턴스에 최신 보안 패치와 소프트웨어 업그레이드를 적용해야 하는 경우, UpdateNotebookInstanceLifecycleConfig API를 통해 라이프사이클 구성을 업데이트하여 인스턴스 재시작 시 자동으로 업데이트를 적용할 수 있다.

## 📝 설명

### SageMaker Notebook Instance Lifecycle Configuration이 보안 업그레이드에 적합한 이유

SageMaker 노트북 인스턴스는 기본적으로 생성 시점의 AMI와 소프트웨어 스택을 사용합니다. 시간이 지나면서 새로운 보안 패치, Jupyter 버전 업데이트, 패키지 업그레이드 등이 필요하게 되는데, 단순한 인스턴스 재시작으로는 이러한 업데이트가 적용되지 않습니다.

Lifecycle Configuration을 통해 OnStart 스크립트를 정의하면, 인스턴스가 시작될 때마다 최신 보안 패치와 소프트웨어 업그레이드를 자동으로 설치할 수 있습니다.

### 아키텍처 플로우

```
기존 Notebook Instance
           ↓
UpdateNotebookInstanceLifecycleConfig API 호출
           ↓
OnStart Script 업데이트 (보안 패치 + 소프트웨어 업그레이드)
           ↓
Notebook Instance 재시작
           ↓
업데이트된 Lifecycle Configuration 실행
           ↓
최신 보안 패치 및 소프트웨어 적용 완료
```

### Trade-offs 고려사항

**UpdateNotebookInstanceLifecycleConfig 장점**:
- 기존 인스턴스와 데이터 보존
- 자동화된 업그레이드 프로세스
- 비용 효율적 (새 인스턴스 생성 불필요)
- 스크립트 기반으로 세밀한 제어 가능

**UpdateNotebookInstanceLifecycleConfig 단점**:
- 버전 호환성 문제 발생 가능
- 업그레이드 중 일시적 다운타임
- 기존 코드가 새 환경에서 실행 안 될 위험
- 롤백 시 복잡한 과정 필요

**새 인스턴스 생성 + EBS 마운트 장점**:
- 깨끗한 최신 환경 보장
- 기존 인스턴스를 백업으로 유지 가능
- 문제 발생 시 빠른 롤백

**새 인스턴스 생성 + EBS 마운트 단점**:
- 복잡한 데이터 마이그레이션 과정
- 추가 비용 발생
- 설정 재구성 필요

## 🔍 주요개념

### Lifecycle Configuration vs 인스턴스 교체

- **Lifecycle Configuration**: 인스턴스 시작/종료 시 실행되는 스크립트를 정의하여 환경을 자동으로 구성
- **인스턴스 교체**: 완전히 새로운 인스턴스를 생성하여 기존 환경을 대체하는 방식

### 실전 적용

- **정기 보안 패치 적용**: OnStart 스크립트에서 `yum update`, `pip upgrade` 등 실행
- **개발 환경 표준화**: 팀 전체가 동일한 패키지 버전과 설정 사용
- **컴플라이언스 요구사항 충족**: 보안팀의 최신 업데이트 적용 요구사항 만족

## 📝 관련 문제

**Question:** A data scientist has been operating an Amazon SageMaker notebook instance for many weeks. During this time, a new edition of Jupyter Notebook was introduced, along with several software upgrades. The security team requires that all SageMaker notebook instances operate with the most recent security and software upgrades available from SageMaker. How should the data scientist satisfy these requirements?

**Options:**

- A) Call the CreateNotebookInstanceLifecycleConfig API operation
- B) Create a new SageMaker notebook instance and mount the Amazon Elastic Block Store (Amazon EBS) volume from the original instance
- C) Stop and then restart the SageMaker notebook instance
- D) Call the UpdateNotebookInstanceLifecycleConfig API operation

**정답: D) UpdateNotebookInstanceLifecycleConfig API operation**

💡 추가 설명:

- **A) CreateNotebookInstanceLifecycleConfig** - 새로운 라이프사이클 구성을 생성할 뿐, 기존 인스턴스에 적용되지 않음
- **B) 새 인스턴스 생성 + EBS 마운트** - 가능하지만 불필요하게 복잡하고 데이터 마이그레이션 위험 존재
- **C) 단순 재시작** - 기본 AMI나 소프트웨어 스택 업데이트 없이는 보안 패치가 적용되지 않음

### 🚨 실무 고려사항

**버전 호환성 검증이 필수:**
- Jupyter Notebook 새 버전으로 인한 기존 .ipynb 파일 호환성 확인
- Python/R 패키지 버전 변경으로 인한 기존 코드 동작 검증
- 라이브러리 의존성 충돌 가능성 사전 테스트

**권장 운영 프로세스:**
1. 테스트 환경에서 먼저 업그레이드 검증
2. 기존 환경 백업 (`pip freeze > requirements.txt`)
3. 단계적 업그레이드 적용
4. 롤백 계획 준비