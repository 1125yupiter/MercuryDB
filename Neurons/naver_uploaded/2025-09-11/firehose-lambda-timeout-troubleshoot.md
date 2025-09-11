---

title: kinesis-firehose-lambda-timeout-troubleshooting
created: 2025-01-30
modified: 2025-01-30
tags:
- service/kinesis-firehose
- service/lambda
- problem/data-transformation
- constraint/timeout
- troubleshooting/streaming-data
aliases: ["firehose-lambda-timeout", "firehose-timeout", "data-transformation-timeout"]

---

# Kinesis Data Firehose Lambda Timeout으로 인한 데이터 전송 실패 문제

## 🎯 핵심 포인트

Kinesis Data Firehose에서 Data Transformation을 사용할 때 Lambda 함수의 기본 timeout(3초)이 배치 처리 시간을 초과하는 경우, Lambda 함수가 강제 종료되어 S3로 데이터가 전송되지 않는 문제가 발생할 수 있다.

## 📝 설명

### Kinesis Data Firehose Data Transformation 아키텍처

Kinesis Data Firehose의 Data Transformation 기능을 활성화하면, Firehose가 자동으로 Lambda 함수를 호출하여 데이터를 변환한다. 이 과정에서 Lambda의 timeout 설정이 중요한 역할을 한다.

**핵심 동작 원리:**
- Firehose는 버퍼 조건(크기 또는 시간)이 만족되면 배치 단위로 Lambda 함수를 호출
- Lambda 함수는 받은 레코드들을 변환 처리
- 변환이 완료되면 Firehose가 결과를 S3로 전송

### 아키텍처 플로우

```
스트리밍 데이터 → Kinesis Data Firehose → 버퍼링 (기본: 5MB/60초) 
                                        ↓
S3 버킷 ← 변환된 데이터 ← Lambda 함수 (기본 timeout: 3초)
```

**문제 발생 시나리오:**
```
1. 버퍼 조건 만족 (예: 500개 레코드, 5MB)
2. Lambda 함수 호출 + 3초 타이머 시작
3. 데이터 변환 작업 진행 중...
4. 3초 경과 → AWS가 Lambda 강제 종료
5. 변환 실패 → S3에 데이터 미도달
```

### 기본 설정값과 문제점

**Firehose 기본 버퍼 설정:**
- Buffer Size: 1-128MB (권장: 5MB)
- Buffer Interval: 60-900초 (권장: 60초)

**Lambda 기본 설정:**
- Timeout: 3초 (최대 15분까지 설정 가능)
- Memory: 128MB
- 처리 방식: 배치 단위 (100-500개 레코드)

**실제 처리 시간 예시:**
```
배치 크기: 500개 레코드
레코드당 변환 시간: 0.02초
총 필요 시간: 10초
기본 timeout: 3초 → 실패!
```

### Trade-offs 고려사항

**Lambda Timeout 증가의 장점:**
- 데이터 변환 성공률 향상
- 복잡한 변환 로직 처리 가능
- 외부 API 호출 등 추가 작업 수행 가능

**Lambda Timeout 증가의 단점:**
- 비용 증가 (실행 시간에 비례)
- 장애 시 복구 시간 증가
- 메모리 사용량도 함께 고려 필요

**Alternative: Step Functions 사용**
- 15분 이상의 복잡한 워크플로우 필요시
- 여러 단계로 나누어 처리
- 더 높은 복잡도와 비용

**Alternative: Kinesis Data Streams + 별도 처리**
- 실시간 처리 필요시
- Lambda timeout 제약 회피
- 더 복잡한 아키텍처 관리

## 🔍 주요개념

### Timeout vs Buffer 설정 비교

- **Lambda Timeout**: 하나의 배치를 처리하는 최대 시간 (1초-15분)
- **Firehose Buffer**: 배치를 생성하는 조건 (크기: 1-128MB, 시간: 60-900초)
- **배치 처리**: Firehose가 여러 레코드를 묶어서 Lambda로 한 번에 전송

### 실전 적용

- **로그 데이터 파싱**: JSON 파싱 및 필드 추출 (권장 timeout: 1-3분)
- **데이터 정제 및 필터링**: 불필요한 데이터 제거 (권장 timeout: 2-5분)
- **외부 API 연동**: 데이터 enrichment 작업 (권장 timeout: 5-10분)

## 📝 관련 문제

**Question:** You are deploying your data streaming pipeline for your machine learning environment. Your cloud formation stack has a Kinesis Data Firehose using the Data Transformation feature where you have configured Firehose to write to your S3 data lake. When you stream data through your Kinesis Firehose, you notice that no data is arriving your S3 bucket. What might be the problem that is causing the failure?

**Options:**

- A) Your lambda memory setting is set to the maximum value allowed.
- B) Your S3 bucket is in the same region as your Kinesis Data Firehose.
- C) Your Kinesis Data Firehose buffer setting is set to the default value.
- D) Your lambda timeout value is set to the default value.

**정답: D) Your lambda timeout value is set to the default value.**

💡 추가 설명:

- **Option A** - Lambda의 최대 메모리(3GB) 설정은 오히려 CPU 성능을 향상시켜 처리 속도를 높임. 데이터 전송 실패의 원인이 아니며 단지 비용만 증가
- **Option B** - S3 버킷과 Firehose가 같은 리전에 있는 것은 권장사항이며 정상적인 설정. 다른 리전에 있어도 정상 작동함
- **Option C** - Firehose의 기본 버퍼 설정(1-128MB, 60-900초)은 정상적으로 작동하며 S3 전송 실패의 원인이 아님
- **핵심 원인** - Lambda의 기본 timeout(3초)은 대부분의 배치 처리에 부족하며, 이로 인해 함수가 강제 종료되어 데이터가 S3에 도달하지 못함

## 🔧 해결 방법

### 즉시 해결책

1. **Lambda 콘솔에서 timeout 증가**
   - 기본 3초 → 30초, 1분, 또는 필요한 시간으로 설정
   - 처리 복잡도에 따라 조정

2. **CloudWatch 로그 확인**
   - Lambda 함수의 실행 로그에서 timeout 오류 확인
   - Duration 메트릭으로 실제 처리 시간 파악

### 장기적 최적화

1. **모니터링 설정**
   - Lambda Duration, Errors 메트릭 추적
   - Firehose DeliveryToS3.Success 비율 모니터링

2. **성능 튜닝**
   - 배치 크기와 Lambda 성능의 균형점 찾기
   - 메모리 설정으로 CPU 성능 조정

3. **비용 최적화**
   - 실제 사용량 기반으로 timeout 재조정
   - 불필요하게 긴 timeout 설정 방지

---

*트러블슈팅 팁: Firehose Data Transformation 문제의 80%는 Lambda timeout 설정이 원인입니다. 다른 원인을 찾기 전에 먼저 Lambda timeout을 확인하세요.*