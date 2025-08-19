---

title: iam-sagemaker-access-control
created: 2025-08-19
modified: 2025-08-19
tags:
- service/sagemaker
- security/iam-policy
- access-control/group-based
- ml-ops/notebook-management
- aws/identity-management
aliases: ["IAM-SageMaker", "notebook-access", "ml-security"]

---

# IAM을 통한 SageMaker 노트북 인스턴스 접근 제어

## 🎯 핵심 포인트

특정 IAM 그룹만 SageMaker 노트북 인스턴스에 접근하도록 제한하려는 경우, Identity-based Policy를 사용하여 서비스 수준에서 권한을 제어할 수 있다.

## 📝 설명

### Identity-based Policy가 SageMaker 접근 제어에 적합한 이유

Identity-based Policy는 IAM 사용자, 그룹, 또는 역할에 직접 연결되는 정책으로, AWS 서비스에 대한 접근 권한을 세밀하게 제어할 수 있습니다. SageMaker 노트북 인스턴스의 경우, 특정 IAM 그룹에만 노트북 생성, 시작, 중지, 접근 등의 권한을 부여하여 보안을 강화할 수 있습니다.

### 아키텍처 플로우

```
IAM User/Group → Identity-based Policy → SageMaker Service API → Notebook Instance
     ↓                    ↓                        ↓                    ↓
[인증/인가]      [권한 검증]           [서비스 접근]         [리소스 사용]
```

### 보안 계층 구분

#### 1. 접근 권한 보안 (IAM Policy 영역)
- **목적**: "누가 이 서비스를 사용할 수 있는가?"
- **제어 대상**: 서비스 API 호출 권한
- **예시**: 노트북 생성, 시작, 중지, 삭제 권한

#### 2. 데이터 보안 (KMS 영역)
- **목적**: "저장된 데이터를 어떻게 보호할 것인가?"
- **제어 대상**: 스토리지 암호화
- **예시**: 노트북 볼륨, 파일 시스템 암호화

### Trade-offs 고려사항

**Identity-based Policy 장점**:
- IAM의 기본 기능으로 간단하고 직관적
- 세밀한 권한 제어 가능
- 조건부 접근 제어 지원
- 다른 AWS 서비스와 일관된 보안 모델

**Identity-based Policy 단점**:
- 복잡한 조직 구조에서는 정책 관리가 복잡해질 수 있음
- 권한 변경 시 즉시 반영되지 않을 수 있음

**KMS 장점**:
- 강력한 데이터 암호화
- 키 순환 자동화
- 세밀한 암호화 키 권한 관리

**KMS 단점**:
- 서비스 접근 권한 제어에는 부적합
- 추가 비용 발생
- 복잡성 증가

## 🔍 주요개념

### 보안 제어 방식 비교

- **Identity-based Policy**: IAM 주체(사용자/그룹/역할)에 연결되어 AWS 서비스 접근 권한을 제어
- **Resource-based Policy**: AWS 리소스에 직접 연결되어 해당 리소스에 대한 접근 권한을 제어
- **KMS**: 데이터 암호화 키 관리를 통한 데이터 보호
- **Network ACL**: 네트워크 계층에서의 트래픽 제어

### 실전 적용

- **ML 팀 전용 노트북**: 특정 부서의 데이터 사이언티스트만 접근 가능한 노트북 환경 구성
- **프로젝트별 격리**: 프로젝트 그룹별로 독립적인 SageMaker 리소스 접근 권한 부여
- **개발/운영 환경 분리**: 개발자와 운영자에게 서로 다른 수준의 SageMaker 권한 부여

## 📝 관련 문제

**Question:** A company aims to enhance the security and management of its machine learning projects by restricting access to Amazon SageMaker notebook instances to certain IAM groups. What method should be employed to ensure that only designated IAM groups have the necessary permissions to access and interact with SageMaker notebooks?

**Options:**

- A) Use AWS Key Management Service (KMS) to encrypt SageMaker notebooks, granting access only to specific IAM groups
- B) Configure a Network Access Control List (NACL) to restrict IP addresses associated with IAM groups
- C) Assign an identity-based policy to SageMaker notebook instances specifying allowed IAM groups
- D) Set up an Amazon Cognito user pool to control access to SageMaker notebooks for IAM groups

**정답: C) Assign an identity-based policy to SageMaker notebook instances specifying allowed IAM groups**

💡 추가 설명:

- **옵션 A (KMS)** - 데이터 암호화 목적으로 서비스 접근 권한 제어와는 별개 계층
- **옵션 B (NACL)** - 네트워크 트래픽 제어용으로 IAM 그룹 기반 접근 제어와 무관
- **옵션 D (Cognito)** - 애플리케이션 사용자 인증용으로 AWS 서비스 접근 제어에 부적합

### 구현 예시

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sagemaker:CreateNotebookInstance",
                "sagemaker:DescribeNotebookInstance",
                "sagemaker:StartNotebookInstance",
                "sagemaker:StopNotebookInstance"
            ],
            "Resource": "arn:aws:sagemaker:*:*:notebook-instance/*",
            "Condition": {
                "StringEquals": {
                    "aws:PrincipalTag/Department": "MLTeam"
                }
            }
        }
    ]
}
```