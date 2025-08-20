---

title: lambda-rds-execution-role
created: 2025-08-20
modified: 2025-08-20
tags:
- service/lambda
- service/rds
- security/iam-role
- deployment/serverless
- database/mysql
aliases: ["lambda-db-access", "execution-role", "rds-auth"]

---

# AWS Lambda RDS 접근을 위한 Execution Role 설정

## 🎯 핵심 포인트

Lambda 함수가 RDS 데이터베이스에 접근해야 하는 경우 Lambda execution role에 적절한 RDS 권한을 부여하면, 별도의 자격증명 없이 안전하게 데이터베이스에 연결할 수 있다.

## 📝 설명

### Lambda Execution Role이 RDS 접근에 적합한 이유

AWS Lambda는 실행 시 반드시 IAM 역할(execution role)을 사용하여 AWS 서비스에 접근합니다. 이 역할에 RDS 접근 권한을 부여하면 다음과 같은 보안상 이점을 얻을 수 있습니다:

- **자격증명 분리**: 데이터베이스 패스워드를 코드에 하드코딩하지 않음
- **자동 권한 관리**: Lambda 함수 삭제 시 권한도 자동으로 해제
- **최소 권한 원칙**: 특정 데이터베이스와 사용자에만 접근 허용
- **감사 추적**: CloudTrail을 통한 접근 로그 자동 기록

### 아키텍처 플로우

```
사용자 → API Gateway/직접 호출 → Lambda 함수 실행
                                      ↓
                               Execution Role 권한 확인
                                      ↓
                            IAM Database Authentication
                                      ↓
                                 RDS MySQL 접근
```

### 권한 체계와 보안 분리

Lambda의 권한 체계는 2단계로 구성됩니다:

1. **사용자 → Lambda**: 사용자 IAM 권한으로 Lambda 호출 가능 여부 확인
2. **Lambda → RDS**: Lambda execution role 권한으로 RDS 접근 가능 여부 확인

이는 사용자가 Lambda를 통해서만 데이터베이스에 접근할 수 있고, 직접적인 RDS 접근은 차단되는 구조를 만듭니다.

### Trade-offs 고려사항

**IAM Database Authentication 장점**:
- 패스워드 관리 불필요
- 자동 토큰 순환으로 보안 강화
- 세밀한 권한 제어 가능
- AWS 통합 보안 모델 활용

**IAM Database Authentication 단점**:
- 초기 설정 복잡성
- RDS에서 IAM 인증 활성화 필요
- 일부 DB 엔진에서 제한적 지원

**Secrets Manager 장점**:
- 모든 DB 엔진 지원
- 기존 데이터베이스 사용자 활용 가능
- 자동 패스워드 순환

**Secrets Manager 단점**:
- 추가 비용 발생
- Secrets Manager 접근 권한 별도 관리
- 네트워크 지연 가능성

## 🔍 주요개념

### 권한 설정 방법 비교

- **IAM Database Authentication**: Lambda execution role에 `rds-db:connect` 권한 부여, 토큰 기반 인증
- **Secrets Manager**: Lambda execution role에 `secretsmanager:GetSecretValue` 권한 부여, 전통적인 ID/PW 방식
- **환경변수 저장**: 보안상 권장하지 않음 (코드 접근자가 자격증명 확인 가능)
- **보안그룹 오픈**: Lambda는 고정 IP가 없어 비현실적

### 실전 적용

- **데이터 분석 파이프라인**: S3 데이터 → Lambda 처리 → RDS 결과 저장
- **실시간 API 서비스**: API Gateway → Lambda → RDS 조회 → JSON 응답
- **배치 처리 작업**: CloudWatch Events → Lambda → 대량 데이터 RDS 업데이트

## 📝 관련 문제

**Question:** You are developing a machine learning model and want to deploy it as an AWS Lambda function. The model requires access to a MySQL database hosted on Amazon RDS. Which of the following options would be the best way to provide the necessary database access to the Lambda function?

**Options:**

- A) Create an IAM user with database permissions and provide its credentials to the Lambda function
- B) Grant the necessary database permissions to the AWS Lambda execution role
- C) Store the database credentials in an environment variable within the Lambda function code
- D) Open the RDS security group to allow inbound traffic from the Lambda function's IP address

**정답: B) Grant the necessary database permissions to the AWS Lambda execution role**

💡 추가 설명:

- **A) IAM 사용자 생성** - 불필요한 보안 위험과 관리 오버헤드 발생, 자격증명 관리 복잡성 증가
- **C) 환경변수 저장** - 보안상 위험 (함수 코드 접근자가 자격증명 확인 가능), 하드코딩된 자격증명 문제
- **D) 보안그룹 오픈** - Lambda는 고정 IP가 없어서 비현실적, 네트워크 레벨 접근 제어로는 부족