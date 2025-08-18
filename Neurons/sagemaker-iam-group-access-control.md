---

title: sagemaker-iam-group-access-control
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker
- security/iam-groups
- access-control/permissions
- policy/group-based
- ml/notebook-instances
aliases: ["sagemaker-security", "iam-group-control", "notebook-access"]

---

# SageMaker 노트북 인스턴스 IAM 그룹 기반 접근 제어

## 🎯 핵심 포인트

ML 프로젝트의 보안을 강화하고자 하는 경우 SageMaker 노트북 인스턴스에서, 특정 IAM 그룹에만 접근 권한을 부여하는 IAM 정책을 생성하여 해당 그룹에 연결할 수 있다.

## 📝 설명

### IAM 정책이 SageMaker 접근 제어에 적합한 이유

IAM 정책을 통한 그룹 기반 접근 제어는 AWS의 핵심 보안 원칙인 최소 권한(Principle of Least Privilege)을 구현하는 가장 직접적이고 효과적인 방법입니다. 이 방식은 사용자를 IAM 그룹에 할당하고, 그룹에 정책을 연결함으로써 중앙집중식 권한 관리를 가능하게 합니다.

### 아키텍처 플로우

```
사용자 → IAM 그룹 → IAM 정책 → SageMaker 권한
  ↓         ↓          ↓           ↓
User1   ML-Team   SageMaker-   CreateNotebook
User2             Policy       StartNotebook
User3                         StopNotebook
                              DescribeNotebook
```

### IAM 정책 예시

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sagemaker:CreateNotebookInstance",
        "sagemaker:StartNotebookInstance",
        "sagemaker:StopNotebookInstance",
        "sagemaker:DescribeNotebookInstance",
        "sagemaker:CreatePresignedNotebookInstanceUrl"
      ],
      "Resource": "*"
    }
  ]
}
```

### Trade-offs 고려사항

**IAM 정책 기반 접근 제어 장점**:
- 정확한 그룹 단위 권한 관리
- 세밀한 권한 제어 가능
- AWS 보안 모범 사례 준수
- 확장성과 유지보수성 우수

**IAM 정책 기반 접근 제어 단점**:
- 초기 설정 복잡성
- 정책 관리 오버헤드
- 권한 변경 시 정책 수정 필요

**SageMaker Studio + SSO 장점**:
- 중앙집중식 사용자 관리
- 통합된 개발 환경 제공
- 기업 ID 시스템과의 연동

**SageMaker Studio + SSO 단점**:
- IAM 그룹 기반 직접 제어 제한
- 추가 SSO 구성 복잡성
- 요구사항과의 부분적 불일치

## 🔍 주요개념

### 접근 제어 방식 비교

- **IAM 정책**: 특정 AWS 서비스에 대한 권한을 JSON 형태로 정의하는 문서
- **IAM 역할**: 서비스가 다른 AWS 리소스에 접근할 때 사용하는 임시 자격 증명
- **보안 그룹**: 네트워크 레벨에서의 접근 제어 (방화벽 역할)
- **VPC 엔드포인트**: AWS 서비스에 대한 프라이빗 연결

### 실전 적용

- **ML 팀 구성**: 데이터 사이언티스트, ML 엔지니어를 별도 그룹으로 관리하여 각각 다른 SageMaker 권한 부여
- **프로젝트별 권한**: 특정 프로젝트팀만 해당 노트북 인스턴스에 접근할 수 있도록 리소스 기반 정책 적용
- **환경별 분리**: 개발, 스테이징, 프로덕션 환경별로 다른 IAM 그룹과 권한 설정

## 📝 관련 문제

**Question:** A company aims to enhance the security and management of its machine learning projects by restricting access to Amazon SageMaker notebook instances to certain IAM groups. What method should be employed to ensure that only designated IAM groups have the necessary permissions to access and interact with SageMaker notebooks?

**Options:**

- A) Use Amazon SageMaker Studio with an AWS Single Sign-On (SSO) configuration that restricts access to SageMaker notebooks based on the designated IAM groups
- B) Create an IAM policy with specific permissions for SageMaker, and attach it to the designated IAM groups, ensuring only members of those groups have the necessary permissions
- C) Create a custom SageMaker policy that grants the required permissions, and attach the policy to an IAM role assigned to the SageMaker notebook instance
- D) Attach a VPC endpoint to the SageMaker notebook instance and configure security groups for the appropriate IAM groups

**정답: B) Create an IAM policy with specific permissions for SageMaker, and attach it to the designated IAM groups**

💡 추가 설명:

- **Option A (SSO 구성)** - 유효한 방법이지만 IAM 그룹 기반 직접 권한 제어가 아닌 SSO 사용자 중심 접근
- **Option C (IAM 역할 연결)** - 노트북 인스턴스의 실행 권한 관리이며, 사용자의 접근 권한 제어가 아님
- **Option D (VPC 엔드포인트)** - 네트워크 레벨 접근 제어로 IAM 권한 관리와는 별개의 보안 계층