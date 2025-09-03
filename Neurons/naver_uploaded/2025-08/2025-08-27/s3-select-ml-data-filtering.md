---

title: s3-select-ml-data-filtering
created: 2025-08-19
modified: 2025-08-19
tags:
- service/s3-select
- constraint/data-transfer
- technique/serverless-query
- usecase/ml-preprocessing
- format/structured-data
aliases: ["s3select", "serverless-query", "data-filtering"]

---

# Amazon S3 Select를 활용한 머신러닝 데이터 전처리

## 🎯 핵심 포인트

대용량 구조화된 데이터를 머신러닝 워크플로에서 전처리해야 하는 경우, Amazon S3 Select를 사용하여 전체 데이터셋을 다운로드하지 않고도 필요한 데이터만 필터링하고 추출할 수 있다.

## 📝 설명

### Amazon S3 Select가 머신러닝 데이터 전처리에 적합한 이유

Amazon S3 Select는 SQL 표현식을 사용하여 S3에 저장된 객체의 일부분만 검색할 수 있는 서버리스 쿼리 서비스입니다. **엑셀 파일 하나를 열어서 필터링하는 것**처럼 단일 파일에서 필요한 데이터만 빠르게 추출할 수 있습니다. 머신러닝 워크플로에서 대용량 데이터셋을 다루는 경우, 전체 데이터를 컴퓨팅 인스턴스로 전송하는 대신 S3에서 직접 필요한 데이터만 추출하여 네트워크 비용과 처리 시간을 대폭 절약할 수 있습니다.

### 아키텍처 플로우

```
Raw Data (S3) → S3 Select (SQL Query) → Filtered Data → ML Pipeline
      ↓                    ↓                  ↓              ↓
   TB급 데이터         단일 파일 쿼리        필요한 데이터만    SageMaker/
   (CSV/JSON/         (엑셀 필터처럼        GB급으로 축소     컴퓨팅 인스턴스
   Parquet)          WHERE, SELECT)                              

vs

Multiple Files → Athena (Complex SQL) → Joined Results → Analysis
     ↓               ↓                      ↓              ↓
여러 테이블         VLOOKUP처럼            통합 분석        비즈니스 인사이트
(고객+주문+상품)    조인 및 그룹핑          결과셋
```

### Trade-offs 고려사항

**Amazon S3 Select 장점**:
- 단일 파일 빠른 필터링 (엑셀 필터 기능처럼)
- 네트워크 전송량 최대 80% 감소
- 서버리스 방식으로 인프라 관리 불필요
- 즉석 쿼리 실행 (메타데이터 설정 불필요)
- 실제 반환 데이터양만 과금 ($0.002/GB)

**Amazon S3 Select 단점**:
- 단일 파일만 처리 가능 (조인 불가)
- 구조화된 데이터(CSV, JSON, Parquet)만 지원
- 복잡한 집계나 고급 분석 함수 제한
- 실시간 스트리밍 데이터 처리 불가

**Amazon Athena 장점**:
- 여러 파일/테이블 조인 가능 (VLOOKUP처럼)
- 복잡한 SQL 쿼리 및 집계 함수 지원
- ANSI SQL 완전 지원
- 정기 리포팅 및 비즈니스 분석에 적합

**Amazon Athena 단점**:
- 초기 메타데이터 설정 필요 (Glue 카탈로그)
- 스캔된 전체 데이터양에 따른 요금 부과 ($5/TB)
- 단순 필터링에는 과도한 설정과 비용

## 🔍 주요개념

### 지원 데이터 형식 비교

- **CSV**: 구분자 기반 테이블 데이터, 헤더 지원
- **JSON**: 중첩된 객체 구조, JSONPath 표현식 사용
- **Apache Parquet**: 컬럼형 압축 포맷, 고성능 분석용

### 실전 적용

- **단일 파일 빠른 조회**: 100GB 로그 파일에서 에러 로그만 즉석 추출 (엑셀 필터처럼)
- **데이터 미리보기**: TB급 고객 데이터에서 샘플 1000행만 확인하여 데이터 구조 파악
- **ML 전처리 최적화**: 특정 기간의 거래 데이터만 선별하여 추천 모델 훈련 데이터 준비
- **비용 효율적 탐색**: 전체 다운로드 없이 필요한 부분만 추출하여 네트워크 비용 절약

## 📝 관련 문제

**Question:** A machine learning team needs to process a 500GB dataset stored in Amazon S3 for training a customer churn prediction model. The dataset contains customer transaction records in CSV format, but only transactions from the last 6 months are relevant for the model. The team wants to minimize data transfer costs and processing time. Which approach should they use?

**Options:**

- A) Download the entire dataset to Amazon EC2 and filter locally using pandas
- B) Use Amazon S3 Select with SQL WHERE clause to filter data by date range
- C) Use Amazon EMR to process the entire dataset with Spark
- D) Use Amazon Glue ETL job to process the full dataset
- E) Transfer data to Amazon Redshift for filtering and analysis

**정답: B) Amazon S3 Select with SQL WHERE clause to filter data by date range**

💡 추가 설명:

- **Option A** - 전체 500GB 다운로드로 인한 높은 네트워크 비용과 긴 전송 시간
- **Option C** - EMR 클러스터 비용이 발생하며 과도한 리소스 사용
- **Option D** - Glue ETL은 전체 데이터 처리 시 불필요한 비용 발생
- **Option E** - Redshift로의 데이터 이동 시 추가 비용과 시간 소요