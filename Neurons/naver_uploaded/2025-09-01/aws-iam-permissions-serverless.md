---

title: aws-iam-permissions-serverless
created: 2025-08-21
modified: 2025-08-21
tags:
- service/iam
- service/lambda
- service/s3
- concept/permissions
- concept/roles
aliases: ["iam-guide", "aws-permissions", "serverless-security"]

---

# AWS IAM을 통한 서비스 간 권한 관리

## 🎯 핵심 포인트

서버리스 애플리케이션에서 AWS 서비스 간 접근이 필요한 경우, IAM Role을 통해 안전하고 효율적인 권한 관리를 할 수 있다.

## 📝 설명

### IAM Role이 서비스 간 권한 관리에 적합한 이유

IAM Role은 AWS 서비스들이 다른 서비스에 접근할 때 사용하는 핵심 메커니즘입니다. 영구적인 액세스 키 대신 임시 자격 증명을 제공하여 보안성을 높이고, 세밀한 권한 제어가 가능합니다. Lambda 함수가 S3 버킷에 접근하는 경우, IAM Role을 Lambda 함수에 연결하면 AWS가 자동으로 임시 자격 증명을 생성하고 관리합니다.

### 아키텍처 플로우

```
Lambda 함수 생성 시 → IAM Role 연결 → AWS가 임시 자격 증명 생성 → S3 접근 권한 행사 → 함수 종료 시 자격 증명 만료

IAM Role 구성:
1. Trust Policy (누가 이 Role을 assume할 수 있는지)
2. Permission Policy (assume 후 무엇을 할 수 있는지)

사용자 권한 관리:
개별 사용자 → IAM Group 추가 → Group에 Policy 연결 → 권한 행사
또는
IAM Group → Role Assume 권한 → 사용자가 Role Assume → 임시 권한 사용
```

### Trade-offs 고려사항

**IAM Role 장점**:
- 임시 자격 증명으로 보안성 향상
- 액세스 키 관리 불필요
- 자동 로테이션
- 세밀한 권한 제어 가능
- 중앙 집중 관리

**IAM Role 단점**:
- 초기 설정 복잡성
- 권한 디버깅 어려움
- Cross-account 설정 시 추가 구성 필요

**S3 Bucket Policy 장점**:
- 리소스 중심의 명확한 권한 정의
- 특정 조건부 접근 제어 가능

**S3 Bucket Policy 단점**:
- 관리 포인트 분산
- IAM Role만큼 세밀한 제어 어려움
- 대규모 환경에서 관리 복잡

## 🔍 주요개념

### 권한 관리 방식 비교

- **IAM Role**: AWS 서비스가 다른 서비스에 접근할 때 사용하는 임시 권한
- **IAM Group**: 여러 사용자에게 동일한 권한을 부여하기 위한 그룹핑 메커니즘
- **IAM User**: 개별 사용자나 애플리케이션을 위한 영구 자격 증명
- **Resource-based Policy**: 특정 리소스에 대한 접근 권한을 리소스 레벨에서 정의

### 실전 적용

- **Lambda → S3 접근**: Lambda 함수에 S3 읽기/쓰기 권한이 있는 IAM Role 연결
- **개발팀 권한 관리**: 개발자들을 "Developers" IAM Group에 추가하고, 개발 환경 리소스 접근 권한 부여
- **임시 관리자 권한**: 특별한 작업을 위해 STS를 통해 시간 제한된 관리자 Role Assume

## 📝 관련 문제

**Question:** Your serverless application running on AWS Lambda needs to access a large dataset stored in an S3 bucket. What is the most secure and efficient way to provide this access?

**Options:**

- A) Use IAM roles to grant access to the S3 bucket
- B) Use Amazon Kinesis Firehose to stream the data to the Lambda function  
- C) Create an S3 bucket policy to grant access to the Lambda function
- D) Use Amazon SQS to queue the data for the Lambda function to process
- E) Store AWS access keys in Lambda environment variables

**정답: A) Use IAM roles to grant access to the S3 bucket**

💡 추가 설명:

- **Option B (Kinesis Firehose)** - 실시간 스트리밍용 서비스로, 이미 S3에 저장된 대용량 데이터 접근에는 비효율적
- **Option C (S3 Bucket Policy)** - 가능하지만 IAM Role만큼 세밀한 제어가 어렵고 관리가 복잡함
- **Option D (SQS)** - 메시지 큐잉 서비스로, S3 데이터 직접 접근 용도가 아님
- **Option E (Access Keys)** - 보안상 매우 위험하고 키 관리 부담이 큼