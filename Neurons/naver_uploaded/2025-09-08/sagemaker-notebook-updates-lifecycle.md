---
title: sagemaker-notebook-updates-lifecycle
created: 2025-08-25
modified: 2025-08-25
tags:
- service/sagemaker
- component/notebook-instance
- operation/software-updates
- config/lifecycle-configuration
- maintenance/security-patches
aliases: ["sagemaker-updates", "notebook-updates", "lifecycle-config"]
---

# SageMaker 노트북 인스턴스 소프트웨어 업데이트와 라이프사이클 설정

## 🎯 핵심 포인트

SageMaker 노트북 인스턴스에서 AWS 관리 소프트웨어 업데이트가 필요한 경우 Stop/Restart로 적용하고, 사용자 정의 환경 설정이 필요한 경우 Lifecycle Configuration을 사용할 수 있다.

## 📝 설명

### SageMaker 노트북 인스턴스가 소프트웨어 업데이트에 적합한 이유

SageMaker 노트북 인스턴스는 완전 관리형 서비스로서 AWS가 주기적으로 보안 패치, 커널 업데이트, AWS SDK 업데이트 등을 제공합니다. 이러한 시스템 레벨 업데이트는 인스턴스가 InService 상태일 때는 적용되지 않으며, Stop/Restart 과정을 통해서만 적용됩니다. 반면 사용자 정의 패키지 설치나 환경 설정은 Lifecycle Configuration을 통해 자동화할 수 있습니다.

### 아키텍처 플로우

```
AWS 소프트웨어 업데이트:
노트북 인스턴스 (InService) 
    ↓ Stop
노트북 인스턴스 (Stopped)
    ↓ Start (업데이트 적용)
노트북 인스턴스 (InService) + 최신 소프트웨어

사용자 정의 설정:
CreateNotebookInstanceLifecycleConfig
    ↓ OnCreate/OnStart 스크립트 정의
노트북 인스턴스에 연결
    ↓ 인스턴스 시작 시
스크립트 자동 실행 (Root 권한)
    ↓ 5분 이내
사용자 정의 환경 구성 완료
```

### Trade-offs 고려사항

**Stop/Restart 장점**:
- AWS 공식 권장 방법
- 자동으로 최신 보안 패치 적용
- 추가 설정 불필요
- 안정적이고 신뢰할 수 있음

**Stop/Restart 단점**:
- 다운타임 발생
- 사용자 정의 설치 패키지 초기화
- 일시적인 작업 중단

**Lifecycle Configuration 장점**:
- 사용자 정의 환경 자동 구성
- 재시작 후 일관된 환경 유지
- 여러 인스턴스에 재사용 가능
- 스크립트 버전 관리 가능

**Lifecycle Configuration 단점**:
- AWS 시스템 업데이트와는 별개
- 스크립트 작성 및 디버깅 필요
- 5분 실행 시간 제한
- Root 권한으로 실행되는 보안 고려사항

## 🔍 주요개념

### 업데이트 유형별 비교

- **AWS 관리 업데이트**: 보안 패치, 커널 업데이트, AWS SDK 등 - Stop/Restart로 적용
- **사용자 정의 설정**: 패키지 설치, 환경 변수, 파일 시스템 마운트 등 - Lifecycle Configuration으로 자동화

### 실전 적용

- **보안팀 요구사항**: 정기적인 보안 패치 적용을 위해 월 1회 인스턴스 재시작
- **개발팀 워크플로우**: 특정 ML 라이브러리와 설정이 필요한 경우 Lifecycle Configuration으로 자동화
- **규제 환경**: 소프트웨어 버전 고정이 필요한 경우 인스턴스를 중지하지 않고 지속 실행 (단, 90일 강제 업데이트 주의)

## 📝 관련 문제

**Question:** For many weeks, a data scientist has been operating an Amazon SageMaker notebook instance. During this time period, a new edition of Jupyter Notebook was introduced, in addition to several software upgrades. The security team requires that all SageMaker notebook instances be operating with the most recent security and software upgrades available from SageMaker. How should the data scientist satisfy these criteria?

**Options:**

- A) Call the CreateNotebookInstanceLifecycleConfig API operation
- B) Create a new SageMaker notebook instance and mount the Amazon Elastic Block Store (Amazon EBS) volume from the original instance
- C) Stop and then restart the SageMaker notebook instance
- D) Call the UpdateNotebookInstanceLifecycleConfig API operation

**정답: C) Stop and then restart the SageMaker notebook instance**

💡 추가 설명:

- **A) CreateNotebookInstanceLifecycleConfig** - 사용자 정의 스크립트 생성용이며, AWS 관리 시스템 업데이트와는 별개
- **B) 새 인스턴스 생성 + EBS 마운트** - 불필요하게 복잡하고 비용이 많이 드는 방법
- **D) UpdateNotebookInstanceLifecycleConfig** - 기존 사용자 정의 스크립트 수정용이며, 시스템 업데이트와 무관

---