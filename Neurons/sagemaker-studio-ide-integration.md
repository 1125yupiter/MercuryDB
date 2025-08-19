---

title: sagemaker-studio-ide-integration
created: 2025-08-19
modified: 2025-08-19
tags:
- service/sagemaker-studio
- development/ide-integration
- tools/vscode
- tools/pycharm
- feature/remote-development
aliases: ["sagemaker-ide", "studio-ide", "third-party-dev-tools"]

---

# SageMaker Studio와 서드파티 IDE 통합

## 🎯 핵심 포인트

개발자가 익숙한 로컬 IDE(VS Code, PyCharm)를 사용하면서 SageMaker Studio의 확장 가능한 컴퓨팅 리소스에 액세스할 수 있다.

## 📝 설명

### SageMaker Studio가 서드파티 IDE 통합에 적합한 이유

SageMaker Studio는 2025년 7월부터 VS Code 원격 연결을 공식 지원하기 시작했으며, PyCharm과 같은 다른 IDE들도 SSH Helper를 통해 연동이 가능하다. 이를 통해 개발자들은 로컬 환경의 커스텀 설정, 확장 프로그램, 단축키를 그대로 유지하면서 클라우드의 강력한 컴퓨팅 자원을 활용할 수 있다.

### 아키텍처 플로우

```
Local IDE (VS Code/PyCharm) 
    ↓ (AWS Toolkit/SSH)
AWS Systems Manager (SSM)
    ↓ (Secure Connection)
SageMaker Studio Development Environment
    ↓ (Kernel Connection)
SageMaker Distribution/Custom Images
    ↓ (Compute Resources)
ML.t3.medium ~ ML.p4d.24xlarge instances
```

### Trade-offs 고려사항

**VS Code 통합 장점**:
- 2025년 7월부터 공식 지원으로 안정성 보장
- AWS Toolkit extension을 통한 간편한 연결
- Deep link를 통해 Studio에서 직접 VS Code 실행 가능
- 기존 확장 프로그램과 설정 그대로 사용

**VS Code 통합 단점**:
- 현재 US East (Ohio) 리전에서만 사용 가능
- 네트워크 지연으로 인한 응답 시간 증가 가능
- 로컬 환경 설정이 복잡할 수 있음

**PyCharm 통합 장점**:
- SageMaker SSH Helper를 통한 강력한 원격 디버깅
- Professional Edition의 고급 디버깅 기능 활용 가능
- Python 전용 IDE로 ML 개발에 특화된 기능

**PyCharm 통합 단점**:
- SSH Helper 설정이 VS Code 대비 복잡
- Professional Edition 라이선스 필요
- 공식 지원이 아닌 커뮤니티 솔루션

## 🔍 주요개념

### 핵심 연결 방식 비교

- **AWS Toolkit Extension**: VS Code에서 SageMaker Studio 공간을 직접 브라우징하고 연결
- **Deep Link**: SageMaker Studio 웹 인터페이스에서 "Open in VS Code" 버튼 클릭
- **SSH Helper**: PyCharm과 VS Code 모두에서 SSH를 통한 원격 연결 및 디버깅
- **Code-Server**: 브라우저에서 VS Code를 직접 실행하는 방식

### 실전 적용

- **모델 개발 초기 단계**: 로컬 IDE에서 빠른 프로토타이핑 후 SageMaker 인프라로 스케일업
- **원격 디버깅**: 대용량 데이터 처리 중 발생하는 오류를 로컬 IDE에서 실시간 디버깅
- **팀 협업**: 각 개발자가 선호하는 IDE를 사용하면서 동일한 SageMaker 환경에서 작업

## 📝 관련 문제

**Question:** A data science team wants to use their preferred local development environments (VS Code and PyCharm) while leveraging AWS SageMaker's scalable compute resources for machine learning model development. Which statement is correct regarding IDE integration with SageMaker Studio?

**Options:**

- A) SageMaker Studio only supports Jupyter notebooks and cannot integrate with external IDEs
- B) PyCharm integration requires purchasing additional AWS services beyond standard SageMaker pricing
- C) SageMaker Studio supports integration with third-party development tools such as PyCharm and Visual Studio Code
- D) IDE integration is only possible through manual SSH configuration without official AWS support
- E) VS Code integration requires installing SageMaker Studio locally on each developer's machine

**정답: C) SageMaker Studio supports integration with third-party development tools such as PyCharm and Visual Studio Code**

💡 추가 설명:

- **Option A** - SageMaker Studio는 Jupyter뿐만 아니라 Code Editor(VS Code OSS 기반)도 내장 지원하며, 서드파티 IDE 통합도 가능
- **Option B** - PyCharm 통합은 SSH Helper를 사용하며 추가 AWS 서비스 비용이 발생하지 않음 (PyCharm Professional 라이선스는 별도)
- **Option D** - VS Code는 2025년 7월부터 공식 지원되며, AWS Toolkit을 통해 간편하게 연결 가능
- **Option E** - VS Code는 로컬에 설치된 상태에서 원격으로 SageMaker Studio에 연결하는 방식으로 작동