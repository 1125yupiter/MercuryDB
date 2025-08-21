---

title: kinesis-firehose-lambda-validation
created: 2025-08-21
modified: 2025-08-21
tags:
- service/kinesis-firehose
- service/lambda
- usecase/data-validation
- constraint/real-time
- technique/stream-processing
aliases: ["firehose-validation", "stream-validation", "lambda-transform"]

---

# Kinesis Data Firehose와 Lambda를 활용한 실시간 데이터 검증 및 필터링

## 🎯 핵심 포인트

스트리밍 데이터 파이프라인에서 데이터 검증과 필터링이 필요한 경우 Kinesis Data Firehose에서, AWS Lambda를 데이터 변환 기능으로 사용할 수 있다.

## 📝 설명

### AWS Lambda가 Firehose 데이터 검증에 적합한 이유

AWS Lambda는 Kinesis Data Firehose의 **Data Transformation** 기능과 직접 통합되어 실시간으로 스트리밍 데이터를 처리할 수 있습니다. Firehose가 데이터를 버퍼링하면 자동으로 Lambda 함수를 동기적으로 호출하여 데이터 검증, 필터링, 변환을 수행한 후 결과를 다시 Firehose로 반환합니다.

Lambda 함수는 각 레코드에 대해 다음과 같은 처리 결과를 반환할 수 있습니다:
- **Ok**: 성공적으로 변환된 레코드
- **Dropped**: 의도적으로 제거된 레코드
- **ProcessingFailed**: 변환 실패한 레코드

### 아키텍처 플로우

```
Data Source → Kinesis Data Firehose → Lambda Function → Kinesis Data Firehose → S3
                     ↓                    (검증/필터링)           ↓
                 버퍼링 (1MB)                                변환된 데이터
                 60초 간격                                    최종 저장
```

### Trade-offs 고려사항

**AWS Lambda 장점**:
- Firehose와 네이티브 통합으로 설정 간편
- 서버리스로 자동 스케일링
- 실시간 데이터 변환 및 필터링
- 복잡한 비즈니스 로직 적용 가능
- 6MB 페이로드 지원

**AWS Lambda 단점**:
- 5분 실행 시간 제한
- 콜드 스타트 지연 가능성
- 복잡한 변환 시 비용 증가

**Kinesis Data Analytics 장점**:
- SQL 기반 실시간 분석
- 복잡한 윈도우 함수 지원
- 스트리밍 집계 처리

**Kinesis Data Analytics 단점**:
- Firehose와 직접 통합 불가 (Data Streams 경유 필요)
- 복잡한 아키텍처 구성
- 단순 검증/필터링에는 과도한 기능

## 🔍 주요개념

### 핵심 개념 비교

- **Data Transformation**: Firehose에서 Lambda를 호출하여 데이터를 실시간으로 변환하는 기능
- **Data Analytics**: 별도 서비스로 복잡한 스트림 분석을 수행하지만 Firehose와 직접 연결 불가

### 실전 적용

- **로그 데이터 정제**: 웹 서버 로그에서 민감 정보 마스킹 후 S3 저장
- **IoT 데이터 검증**: 센서 데이터의 유효성 검사 후 잘못된 값 필터링
- **실시간 ETL**: JSON 형태 데이터를 Parquet 형식으로 변환하며 스키마 검증

## 📝 관련 문제

**Question:** You are designing a data pipeline using Amazon Kinesis Data Firehose to ingest and transform streaming data. Your pipeline requires data validation and filtering to ensure that only valid records are stored in Amazon S3. Which of the following services can you use to perform data validation and filtering in Amazon Kinesis Data Firehose?

**Options:**

- A) Amazon Kinesis Data Analytics
- B) AWS Glue ETL jobs  
- C) AWS Lambda
- D) Amazon SNS
- E) Amazon EMR

**정답: C) AWS Lambda**

💡 추가 설명:

- **Amazon Kinesis Data Analytics** - 스트리밍 데이터 분석용이지만 Firehose와 직접 통합되지 않음
- **AWS Glue ETL jobs** - 배치 ETL 작업용으로 실시간 스트리밍에는 부적합
- **Amazon SNS** - 메시징 서비스로 데이터 변환 기능 없음
- **Amazon EMR** - 대규모 배치 처리용으로 Firehose와 직접 통합 불가