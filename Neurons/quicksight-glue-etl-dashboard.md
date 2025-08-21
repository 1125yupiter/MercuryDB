---

title: quicksight-glue-etl-dashboard
created: 2025-08-21
modified: 2025-08-21
tags:
- service/quicksight
- service/glue
- usecase/dashboard
- constraint/real-time
- technique/etl
aliases: ["qs-etl", "quicksight-etl", "bi-dashboard"]

---

# QuickSight Multi-Source Dashboard with AWS Glue ETL

## 🎯 핵심 포인트

실시간 인사이트가 필요한 e-commerce 대시보드를 구축하는 경우 여러 데이터 소스(Redshift, RDS, S3)에서, AWS Glue ETL로 데이터를 통합하고 QuickSight로 시각화할 수 있다.

## 📝 설명

### AWS Glue ETL이 다중 소스 대시보드에 적합한 이유

AWS Glue는 서로 다른 형태의 데이터 소스들을 통합하는 완전관리형 ETL 서비스입니다. Crawler를 통해 자동으로 데이터 스키마를 발견하고, ETL Job으로 데이터를 변환하여 단일 데이터 스토어에 적재합니다. 이렇게 사전 처리된 데이터를 QuickSight가 조회하면 실시간 성능을 보장할 수 있습니다.

### 아키텍처 플로우

```
1단계: 데이터 발견 및 카탈로그화
Glue Crawler → Redshift 스키마 분석
             → RDS 테이블 구조 파악  
             → S3 파일 포맷 인식
             ↓
        Glue Data Catalog 생성

2단계: ETL 작업으로 데이터 통합
Glue ETL Job → 각 소스에서 데이터 추출 (Extract)
             → 데이터 변환/정제 (Transform)
             → 통합 데이터 스토어에 적재 (Load)
             ↓
        Amazon Redshift (또는 RDS)에 통합 데이터

3단계: 분석 및 시각화
QuickSight → 통합된 단일 데이터 소스 연결
          → 대시보드 생성
          → 사용자와 공유
```

### Trade-offs 고려사항

**AWS Glue ETL 접근법 장점**:

- 사전 변환된 데이터로 빠른 쿼리 성능 보장
- 구조화되고 통합된 스키마로 일관성 제공
- 자동화된 ETL 파이프라인으로 유지보수 효율성
- 대용량 데이터 처리에 적합한 확장성

**AWS Glue ETL 접근법 단점**:

- 초기 ETL 파이프라인 구축 시간 필요
- 데이터 변환 과정에서 약간의 지연 발생
- 추가적인 스토리지 비용 (통합 데이터 스토어)

**QuickSight 직접 연결 장점**:

- 빠른 프로토타이핑과 즉시 시작 가능
- 각 소스의 최신 데이터에 직접 액세스
- 별도 ETL 파이프라인 구축 불필요

**QuickSight 직접 연결 단점**:

- 복잡한 조인 시 성능 저하 발생
- QuickSight 데이터 준비 기능의 제한적 변환 능력
- 여러 소스 간 실시간 쿼리로 인한 응답 지연

## 🔍 주요개념

### 데이터 통합 방식 비교

- **ETL 기반 통합**: 데이터를 사전에 변환하고 통합하여 단일 스토어에 저장하는 방식
- **실시간 연합 쿼리**: 여러 데이터 소스를 실시간으로 조인하여 결과를 반환하는 방식

### 실전 적용

- **대용량 e-commerce 분석**: 주문 데이터(Redshift) + 고객 정보(RDS) + 제품 카탈로그(S3) 통합
- **금융 리스크 대시보드**: 거래 내역 + 고객 프로파일 + 외부 시장 데이터 실시간 분석
- **IoT 센서 모니터링**: 시계열 데이터 + 디바이스 메타데이터 + 알람 로그 통합 시각화

## 📝 관련 문제

**Question:** A company is looking to build a dashboard to visualize real-time insights for their e-commerce platform. They want to use Amazon QuickSight as the BI tool to visualize data from multiple sources, including Amazon Redshift, Amazon RDS, and Amazon S3. Which of the following options describes the best way to achieve this?

**Options:**

- A) Use AWS Glue to crawl and catalog the data sources, and then create a Glue ETL job to transform and load the data into a single data store such as Amazon Redshift or Amazon RDS. Connect QuickSight to the data store and create the dashboard. Share the dashboard with the users.
- B) Use the built-in connectors in Amazon QuickSight to connect to the data sources and combine the data using QuickSight's data preparation capabilities. Publish the dashboard to a group of users in QuickSight.
- C) Export the data from each data source to a common format such as CSV or Parquet, store the files in Amazon S3, and use QuickSight's data preparation capabilities to combine the data. Use Amazon QuickSight Enterprise edition to publish the dashboard to the users.
- D) Use AWS Lambda functions to extract data from each data source, transform it, and load it into a common data store such as Amazon DynamoDB. Use Amazon QuickSight to visualize the data from DynamoDB and create the dashboard. Share the dashboard with the users.

**정답: A) AWS Glue ETL 통합 접근법**

💡 추가 설명:

- **Option B** - QuickSight 데이터 준비 기능은 복잡한 변환 작업에 제한적이며, 여러 소스 실시간 조인 시 성능 저하 발생
- **Option C** - CSV/Parquet 파일 기반 접근법은 대용량 데이터에서 쿼리 지연과 데이터 일관성 유지의 어려움
- **Option D** - Lambda 기반 ETL은 소량 데이터에는 적합하지만 대용량 처리 시 성능 한계와 높은 운영 복잡도