---

title: glue-sagemaker-vpc-integration
created: 2025-08-23 
modified: 2025-08-23 
tags:
- service/glue
- service/sagemaker
- infrastructure/vpc
- development/endpoint
- integration/etl-ml
aliases: ["glue-dev-endpoint", "sagemaker-glue", "vpc-integration"]

---

# VPC 환경에서 AWS Glue와 SageMaker 통합을 위한 Development Endpoint 활용

## 🎯 핵심 포인트

VPC 내 SageMaker 노트북에서 AWS Glue ETL 스크립트에 직접 접근하려는 경우 Glue Development Endpoint를 데이터 사이언스팀의 VPC에 생성하고, 이를 활용하여 SageMaker 노트북을 생성할 수 있다.

## 📝 설명

### AWS Glue Development Endpoint가 VPC 통합에 적합한 이유

AWS Glue Development Endpoint는 "Glue 개발 환경에 접근할 수 있는 지점"으로, VPC 내에서 실시간 Spark 클러스터를 프로비저닝하여 SageMaker 노트북이 Glue 리소스와 직접 통신할 수 있게 한다. 이를 통해 ETL 스크립트를 인터랙티브하게 개발하고 테스트할 수 있으며, API 호출을 통한 간접 접근이 아닌 Spark 컨텍스트를 통한 직접 접근이 가능해진다.

### 아키텍처 플로우

```
데이터 사이언스팀 VPC
├── AWS Glue Development Endpoint (B)
│   ├── Spark 클러스터 프로비저닝
│   └── VPC 내 네트워크 보안 적용
│
├── SageMaker 노트북 인스턴스 (C)
│   ├── Glue Development Endpoint 연결
│   ├── 실시간 Spark 컨텍스트 활용
│   └── ETL 스크립트 직접 개발/테스트
│
└── IAM 권한 설정 (F)
    ├── Glue 작업 실행 권한
    └── SageMaker 훈련 프로세스 트리거 권한
```

### Trade-offs 고려사항

**Glue Development Endpoint 활용 장점**:
- VPC 내에서 ETL 스크립트 직접 개발 가능
- 실시간 Spark 컨텍스트를 통한 인터랙티브 개발 환경
- 네트워크 레벨에서의 직접 접근으로 보안성 확보
- 한 줄씩 코드 실행하며 즉시 결과 확인 가능

**Glue Development Endpoint 활용 단점**:
- 생성 시점에 설정이 결정되어 사후 변경 불가
- 추가적인 인프라 비용 발생
- VPC 설정에 대한 네트워크 지식 필요

**일반 SageMaker 노트북 장점**:
- 설정이 간단하고 빠른 시작 가능
- 비용 효율적

**일반 SageMaker 노트북 단점**:
- boto3 SDK를 통한 간접 API 호출만 가능
- ETL 스크립트 직접 개발/테스트 불가능
- 문제 요구사항인 "직접 접근" 미충족

## 🔍 주요개념

### 접근 방식 비교

- **직접 접근 (Development Endpoint)**: Spark 컨텍스트를 통해 ETL 스크립트를 실시간으로 개발하고 테스트하는 방식
- **간접 접근 (API 호출)**: boto3 SDK를 사용하여 Glue 작업을 실행하지만 스크립트 개발은 별도로 수행하는 방식

### 실전 적용

- VPC 내 민감한 데이터를 처리하는 데이터 사이언스 팀의 ETL 개발 환경 구축
- 실시간 데이터 탐색과 변환 로직을 즉시 검증해야 하는 프로젝트
- 보안 요구사항이 높은 환경에서 Glue와 SageMaker 워크플로우 통합

## 📝 관련 문제

**Question:** A data engineer is using AWS Glue to improve and protect Amazon S3 datasets. The data science team need direct access to the ETL scripts from Amazon SageMaker notebooks contained inside a VPC. The data science team wants to be able to execute the AWS Glue task and trigger the SageMaker training process when this configuration is complete. Which actions should the data engineer perform in combination to achieve these requirements? (Select three.)

**Options:**

- A) Create a SageMaker development endpoint in the data science team's VPC
- B) Create an AWS Glue development endpoint in the data science team's VPC
- C) Create SageMaker notebooks by using the AWS Glue development endpoint
- D) Create SageMaker notebooks by using the SageMaker console
- E) Attach a decryption policy to the SageMaker notebooks
- F) Create an IAM policy and an IAM role for the SageMaker notebooks

**정답: B, C, F) AWS Glue Development Endpoint 활용 및 IAM 권한 설정**

💡 추가 설명:

- **A) SageMaker development endpoint** - AWS에서 존재하지 않는 개념으로, Glue development endpoint와 혼동을 유발하는 오답
- **D) SageMaker 콘솔로 일반 생성** - ETL 스크립트에 직접 접근할 수 없어 문제 요구사항 미충족
- **E) 암호화 정책 연결** - 문제에서 암호화 요구사항이 명시되지 않아 핵심 요구사항과 무관