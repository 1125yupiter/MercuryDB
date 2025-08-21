---

title: aws-lambda-serverless-automation
created: 2025-08-21
modified: 2025-08-21
tags:
- service/lambda
- architecture/serverless
- automation/event-driven
- development/function-based
- integration/aws-services
aliases: ["lambda", "serverless", "faas"]

---

# AWS Lambda를 활용한 서버리스 자동화 아키텍처

## 🎯 핵심 포인트

AWS 관리형 서비스들 간의 연결과 커스텀 로직이 필요한 경우 AWS Lambda에서, 이벤트 기반 함수 실행을 통해 서버 관리 없이 비즈니스 로직을 구현할 수 있다.

## 📝 설명

### AWS Lambda가 서버리스 자동화에 적합한 이유

AWS Lambda는 **"서버 없는 컴퓨팅"**의 핵심 서비스로, 개발자가 서버 관리 없이 코드만 작성하면 됩니다. AWS 생태계에서 Lambda는 **완전관리형 서비스들을 연결하는 접착제** 역할을 수행합니다.

**Lambda의 본질**:
- **이벤트 기반 실행**: 특정 이벤트 발생 시에만 함수 실행
- **완전관리형**: 서버, 스케일링, 가용성을 AWS가 모두 처리
- **다양한 언어 지원**: Python, Node.js, Java, Go, C# 등

**AWS 생태계에서의 역할**:
```
완전관리형 서비스 (S3, RDS, DynamoDB 등)
    ↓ 이벤트 발생
Lambda 함수 (커스텀 로직)
    ↓ 처리 결과
다른 완전관리형 서비스로 전달
```

### 아키텍처 플로우

```
트리거 이벤트 (S3 업로드, API 요청, 스케줄 등)
    ↓
Lambda 함수 실행
    ↓
비즈니스 로직 처리
    ↓
결과를 다른 AWS 서비스로 전달 (SNS, SES, DynamoDB 등)
```

**실제 예시**:
```
S3 파일 업로드 → Lambda → 이미지 리사이징 → 새 S3 버킷 저장
API Gateway 요청 → Lambda → 데이터 계산 → DynamoDB 저장
CloudWatch 알람 → Lambda → 자동 복구 스크립트 → SNS 알림
```

### 개발 방식의 변화

**전통적 개발**:
- 서버 구축 및 관리
- 인프라 설정
- 스케일링 고려
- 운영체제 패치 등

**Lambda 기반 개발**:
1. **요구사항 정의**: 입력, 처리 로직, 출력 명확화
2. **함수 코드 작성**: 비즈니스 로직에만 집중
3. **이벤트 소스 연결**: 트리거 설정
4. **배포**: AWS가 모든 인프라 처리

**현대적 개발 프로세스 (LLM 활용)**:
```python
# 개발자가 LLM에게 요청
"S3에 이미지 업로드되면 썸네일 만들어서 다른 버킷에 저장하는 Lambda 함수 작성해줘"

# LLM이 생성한 완성 코드
import boto3
from PIL import Image

def lambda_handler(event, context):
    # 완전한 비즈니스 로직
    s3 = boto3.client('s3')
    # ... 이미지 처리 로직
    return {'statusCode': 200}
```

### Trade-offs 고려사항

**AWS Lambda 장점**:
- 서버 관리 완전 제거 (NoOps)
- 사용한 만큼만 과금 (밀리초 단위)
- 자동 스케일링 (0에서 수천 개 동시 실행)
- 높은 가용성 및 내결함성
- 빠른 개발 및 배포

**AWS Lambda 단점**:
- 실행 시간 제한 (최대 15분)
- 콜드 스타트 지연시간
- 상태 저장 불가 (Stateless)
- 디버깅 복잡성
- 벤더 종속성

**컨테이너 기반 서비스 (ECS/EKS) 장점**:
- 긴 실행 시간 작업 가능
- 상태 유지 가능
- 더 많은 제어권

**컨테이너 기반 서비스 단점**:
- 서버 관리 필요
- 스케일링 설정 복잡
- 지속적인 운영 비용

## 🔍 주요개념

### Lambda 함수 구조

- **Handler 함수**: `lambda_handler(event, context)` - 진입점
- **Event 객체**: 트리거에서 전달받는 입력 데이터
- **Context 객체**: 실행 환경 정보 (시간 제한, 메모리 등)
- **Return 값**: 호출자에게 반환할 결과

### 트리거 소스 유형

- **동기 호출**: API Gateway, Application Load Balancer
- **비동기 호출**: S3, SNS, CloudWatch Events
- **스트림 기반**: DynamoDB Streams, Kinesis
- **폴링 기반**: SQS

### 실전 적용 시나리오

- **웹 API 백엔드**: API Gateway + Lambda로 REST API 구축
- **데이터 처리 파이프라인**: S3 → Lambda → 데이터 변환 → 분석 서비스
- **자동화 작업**: CloudWatch Events로 정기 실행 (크론 잡 대체)
- **실시간 알림**: 이벤트 발생 시 즉시 SNS/SES로 알림 발송
- **마이크로서비스**: 각 비즈니스 기능을 독립적인 Lambda 함수로 구현

## 📝 관련 문제

**Question:** A company needs to process uploaded images by creating thumbnails and storing metadata whenever files are uploaded to an S3 bucket. The solution should be cost-effective, require minimal infrastructure management, and scale automatically based on upload volume. What AWS service best addresses these requirements?

**Options:**

- A) EC2 instances with Auto Scaling Groups monitoring S3 events
- B) AWS Lambda triggered by S3 upload events
- C) Amazon ECS containers with scheduled tasks
- D) AWS Batch jobs processing S3 objects
- E) Amazon EMR cluster for image processing

**정답: B) AWS Lambda triggered by S3 upload events**

💡 추가 설명:

- **Option A** - EC2는 지속적인 서버 관리와 운영 비용 발생, 과도한 인프라 복잡성
- **Option C** - ECS 컨테이너는 상시 실행으로 비용 비효율적, 이벤트 기반 처리에 부적합
- **Option D** - Batch는 대용량 배치 작업용으로 실시간 이미지 처리에 과도한 솔루션
- **Option E** - EMR은 빅데이터 분석용 클러스터로 단순 이미지 처리에 과도하고 비용 비효율적