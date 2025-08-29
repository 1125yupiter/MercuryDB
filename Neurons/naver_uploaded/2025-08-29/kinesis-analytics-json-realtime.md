---

title: kinesis-analytics-json-realtime
created: 2025-08-20
modified: 2025-08-20
tags:
- service/kinesis-analytics
- data-format/json
- constraint/real-time
- technique/field-extraction
- usecase/iot-streaming
aliases: ["kinesis-json", "streaming-json", "analytics-json"]

---

# Kinesis Data Analytics JSON 데이터 처리 - IoT 스트리밍 환경에서의 효율적인 필드 추출

## 🎯 핵심 포인트

IoT 디바이스에서 JSON 형태로 스트리밍되는 데이터의 특정 필드를 추출하여 집계 및 분석하는 경우, Kinesis Data Analytics의 내장 JSON 함수를 사용하여 실시간으로 필요한 필드만 선택적으로 처리할 수 있다.

## 📝 설명

### Kinesis Data Analytics가 JSON 스트리밍 처리에 적합한 이유

Kinesis Data Analytics는 실시간 스트림 데이터 처리를 위해 설계된 서비스로, JSON 데이터 처리를 위한 내장 SQL 함수들을 제공합니다. `JSON_VALUE()`, `GET_JSON_OBJECT()` 같은 함수를 통해 복잡한 전처리 없이도 스트림에서 직접 필요한 필드를 추출할 수 있어, IoT 환경의 대용량 실시간 데이터 처리에 최적화되어 있습니다.

### 아키텍처 플로우

```
IoT Devices (JSON) → Kinesis Data Streams → Kinesis Data Analytics
                                              ↓
                                        JSON_VALUE() 함수로
                                        필요 필드만 추출
                                              ↓
                                     실시간 집계/분석 처리
                                              ↓
                                    결과 데이터 출력 스트림
```

### Trade-offs 고려사항

**내장 JSON 함수 장점**:
- 실시간 처리로 최소 지연시간 구현
- 추가 서비스나 컴포넌트 없이 단순한 아키텍처
- 스트림 처리 수준에서 직접 필드 선택으로 메모리 효율성
- SQL 기반으로 익숙한 문법과 쉬운 유지보수

**내장 JSON 함수 단점**:
- 복잡한 데이터 변환 로직 구현 제한
- JSON 구조가 매우 복잡한 경우 여러 함수 조합 필요

**AWS Lambda 전처리 장점**:
- 복잡한 비즈니스 로직 구현 가능
- 데이터 검증 및 enrichment 처리
- 프로그래밍 언어의 모든 기능 활용

**AWS Lambda 전처리 단점**:
- 추가 지연시간 발생 (콜드 스타트, 네트워크 호출)
- 추가 비용 및 관리 복잡성
- 실시간 처리 특성 저하

**JSON SerDe 장점**:
- 구조화된 데이터 변환 방식
- 일관된 스키마 관리

**JSON SerDe 단점**:
- 직렬화/역직렬화 오버헤드
- 실시간 처리에서 불필요한 변환 단계

## 🔍 주요개념

### JSON 처리 방식 비교

- **내장 JSON 함수**: 스트림 처리 중 SQL로 직접 필드 추출하는 방식
- **SerDe 변환**: JSON을 테이블 형태로 변환 후 SQL 처리하는 방식
- **Lambda 전처리**: 별도 함수에서 데이터 변환 후 Analytics 입력하는 방식

### 실전 적용

- IoT 센서 데이터에서 온도, 습도만 추출하여 실시간 모니터링
- 디바이스 로그에서 에러 코드와 타임스탬프만 선별하여 장애 분석
- 사용자 활동 JSON에서 핵심 메트릭만 추출하여 실시간 대시보드 구성

## 📝 관련 문제

**Question:** You are designing an Amazon Kinesis Data Analytics application to analyze streaming data from IoT devices. The devices send JSON formatted data including sensor readings and device metadata. You want to extract specific fields from the JSON data for aggregation and analysis. Which approach is most efficient?

**Options:**

- A) Use Kinesis Data Firehose to transform JSON data into tabular format, then ingest into Kinesis Data Analytics
- B) Use built-in JSON functions in Kinesis Data Analytics to extract required fields directly from JSON data
- C) Use JSON SerDe to convert JSON data into tabular format, then apply SQL transformations
- D) Use AWS Lambda to preprocess JSON data and extract required fields before ingesting into Kinesis Data Analytics
- E) Use Amazon Glue to catalog and transform JSON data before processing

**정답: B) Use built-in JSON functions in Kinesis Data Analytics**

💡 추가 설명:

- **Option A (Kinesis Data Firehose)** - 배치 처리 지향 서비스로 실시간 분석에 부적합하며 불필요한 아키텍처 복잡성 증가
- **Option C (JSON SerDe)** - 직접 처리보다 직렬화/역직렬화 오버헤드가 추가되어 효율성 저하
- **Option D (AWS Lambda)** - 콜드 스타트와 네트워크 호출로 인한 지연시간 증가 및 추가 비용 발생
- **Option E (Amazon Glue)** - 배치 ETL 서비스로 실시간 스트리밍 처리에 부적합

---