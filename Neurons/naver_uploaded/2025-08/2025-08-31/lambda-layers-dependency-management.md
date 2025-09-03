---

title: lambda-layers-dependency-management
created: 2025-08-21
modified: 2025-08-21
tags:
- service/lambda
- deployment/layers
- constraint/package-size
- problem/dependency
- technique/library-management
aliases: ["lambda-layers", "aws-layers", "serverless-deps"]

---

# AWS Lambda의 최소환경과 필수적인 Layer 구성 전략

## 🎯 핵심 포인트

Lambda는 최소한의 라이브러리만 기본 제공하므로 실무에서 사용하는 경우 AWS Lambda Layers에서, 필요한 라이브러리를 별도 계층으로 구성하는 것이 거의 필수적이다

## 📝 설명

### Lambda Layers가 실무에서 거의 필수적인 이유

AWS Lambda는 빠른 콜드 스타트와 효율적인 리소스 사용을 위해 정말 최소한의 라이브러리만 기본 제공합니다. 실제로는 표준 라이브러리(json, os, sys, datetime)와 boto3(AWS SDK)만 있고, 일반적으로 사용하는 requests, pandas, numpy 등은 모두 없습니다. 간단한 Lambda 함수라도 API 호출이나 CSV 처리가 필요하면 즉시 추가 라이브러리가 필요하므로, Lambda Layers 구성은 실무에서 거의 필수적입니다.

### Lambda 기본 환경의 현실

```python
# Lambda에서 기본으로 사용 가능한 것들
import json        # ✅ 기본 제공
import boto3       # ✅ AWS SDK
import os, sys     # ✅ 표준 라이브러리  
import datetime    # ✅ 표준 라이브러리
import requests    # ❌ 없음! Layer 필요
import pandas      # ❌ 없음! Layer 필요
import numpy       # ❌ 없음! Layer 필요
```

### 아키텍처 플로우

```
라이브러리 소스 → Layer 생성 → Lambda 함수 연결 → 실행
     ↓              ↓             ↓            ↓
1. pip install   2. ZIP 패키징   3. Layer 연결   4. import 사용
   (로컬/Docker)    Layer 업로드    (최대 5개)     라이브러리 호출
     ↓              ↓             ↓
   - pypi         - /opt/python   - 함수 설정
   - klayers      - 압축 파일     - ARN 지정
   - 커뮤니티      - 250MB 제한    - 버전 관리
```

### Trade-offs 고려사항

**Lambda Layers 장점**:
- 코드와 의존성 분리로 배포 패키지 크기 최적화
- 여러 함수 간 라이브러리 공유 및 재사용
- 라이브러리 업데이트 시 Layer만 교체하면 됨
- 함수별 독립적인 버전 관리 가능

**Lambda Layers 단점**:
- 초기 설정 복잡성 (환경 호환성 고려 필요)
- Layer 생성 및 관리 오버헤드
- Amazon Linux 환경과의 호환성 확인 필요

**함수 코드 직접 포함 단점**:
- 배포 패키지 크기 제한 (50MB compressed, 250MB uncompressed) 초과 위험
- 라이브러리 중복으로 인한 비효율성
- 업데이트 시 모든 함수 재배포 필요

**커스텀 런타임 단점**:
- 단순 라이브러리 추가를 위해서는 과도한 복잡성
- 개발 및 유지보수 비용 증가
- 성능 오버헤드 발생 가능

## 🔍 주요개념

### 라이브러리 소스별 특징

- **직접 생성**: pip install 후 패키징, 완전한 커스터마이징 가능
- **AWS 공식 Layer**: AWSLambda-Python38-SciPy1x 등 검증된 안정성
- **Klayers**: 커뮤니티 제공, 주요 라이브러리 사전 빌드
- **GitHub 커뮤니티**: 오픈소스 Layer, 다양한 조합 제공

### 실전 적용

- **간단한 API 처리도 Layer 필요**: requests 라이브러리 없이는 HTTP 호출 불가
- **기본적인 데이터 처리**: CSV 파일 읽기만 해도 pandas Layer 구성 필요  
- **범용 Layer 템플릿 구성**: requests + pandas + numpy 조합을 기본 Layer로 준비
- **용도별 Layer 전략**: 웹 API용, 데이터 처리용, ML용으로 구분하여 관리

### Layer 없이 가능한 작업 (매우 제한적)

```python
def lambda_handler(event, context):
    # S3 파일 조작 (boto3 사용)
    s3 = boto3.client('s3')
    s3.copy_object(...)
    
    # 기본 JSON 처리
    data = json.loads(event['body'])
    
    # DynamoDB 기본 조작
    dynamodb = boto3.resource('dynamodb')
    table.put_item(Item=data)
```

## 📝 관련 문제

**Question:** Your Lambda function requires pandas and numpy libraries for data processing, but these are not included in the default Lambda environment. The function will be used by multiple teams and needs to handle large datasets efficiently. What is the most appropriate approach to make these libraries available?

**Options:**

- A) Install the libraries as system packages on the Lambda execution environment
- B) Use AWS Lambda Layers to include the libraries in your Lambda function
- C) Embed the libraries directly in your Lambda function deployment package
- D) Create a custom runtime that includes all required libraries
- E) Use EC2 instances instead of Lambda for better library support

**정답: B) Use AWS Lambda Layers to include the libraries in your Lambda function**

💡 추가 설명:

- **A) Install as system packages** - Lambda는 관리형 환경으로 시스템 레벨 설치 불가능, 버전 충돌 위험
- **C) Embed directly in function** - 배포 패키지 크기 제한 초과 위험, 팀 간 중복 라이브러리로 비효율
- **D) Create custom runtime** - 단순 라이브러리 추가를 위해서는 과도한 복잡성, 개발 비용 증가
- **E) Use EC2 instead** - 서버리스 장점 포기, 관리 오버헤드 증가로 부적절

---