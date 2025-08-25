---

title: data-integration-glue-findmatches
created: 2025-08-25
modified: 2025-08-25
tags:
- service/glue
- problem/data-integration
- technique/ml-matching
- constraint/heterogeneous-data
- usecase/retail-catalog
aliases: ["findmatches", "glue-etl", "data-deduplication"]

---

# AWS Glue를 활용한 이종 데이터셋 통합과 중복 제거

## 🎯 핵심 포인트

서로 다른 구조와 형식을 가진 데이터셋을 통합하고 중복을 제거해야 하는 경우 AWS Glue의 FindMatches 변환을 활용하여 ML 기반 지능적 매칭과 데이터 클렌징을 수행할 수 있다.

## 📝 설명

### AWS Glue가 데이터 통합에 적합한 이유

AWS Glue는 완전 관리형 ETL 서비스로, 서로 다른 구조의 데이터를 통합하는데 특화되어 있습니다. 특히 FindMatches 변환은 머신러닝을 활용하여 정확하지 않은 데이터 매칭과 중복 제거를 자동화합니다.

**주요 특징:**
- **자동 스키마 발견**: Glue 크롤러가 다양한 데이터 소스의 구조를 자동으로 파악
- **ML 기반 매칭**: 퍼지 매칭을 통해 완전히 일치하지 않는 레코드도 식별
- **확장성**: 대용량 데이터 처리에 최적화된 서버리스 아키텍처
- **통합 환경**: 데이터 카탈로그, 변환, 작업 스케줄링을 하나의 플랫폼에서 관리

### 아키텍처 플로우

```
[다양한 데이터 소스] → [Glue 크롤러] → [Data Catalog] 
                                            ↓
[S3 결과 저장] ← [Glue ETL Job + FindMatches] ← [스키마 매핑]
                                            ↓
[중복 제거된 통합 데이터] → [분석/BI 도구]
```

### Trade-offs 고려사항

**AWS Glue + FindMatches 장점**:
- ML 기반 자동 매칭으로 수동 작업 최소화
- 서버리스 아키텍처로 인프라 관리 불필요
- 다양한 데이터 소스 지원 (S3, RDS, Redshift 등)
- 데이터 카탈로그 자동 생성으로 메타데이터 관리 용이

**AWS Glue + FindMatches 단점**:
- 복잡한 비즈니스 로직 구현 시 추가 개발 필요
- FindMatches는 주로 중복 제거에 특화, 복잡한 조인 로직은 별도 구현
- 초기 설정과 튜닝에 시간 소요

**AWS Lambda 장점**:
- 간단한 로직 구현 시 빠른 개발 가능
- 세밀한 제어 가능

**AWS Lambda 단점**:
- 대용량 데이터 처리에 부적합 (15분 실행 제한)
- 복잡한 데이터 변환 로직을 직접 구현해야 함
- 확장성 한계

**AWS Lake Formation 장점**:
- 중앙화된 데이터 거버넌스
- 세밀한 보안 제어

**AWS Lake Formation 단점**:
- 데이터 변환이나 매칭 기능 부재
- 주로 데이터 레이크 관리에 특화

## 🔍 주요개념

### 핵심 서비스 비교

- **AWS Glue FindMatches**: ML 기반 레코드 매칭 및 중복 제거 변환
- **AWS Glue ETL**: 데이터 추출, 변환, 로드를 위한 서버리스 서비스
- **AWS Lake Formation**: 데이터 레이크 구축 및 거버넌스 관리 서비스
- **SearchTables API**: Data Catalog 내 테이블 검색용 API (데이터 매칭용 아님)

### 실전 적용

- **소매업체**: 주문 데이터와 제품 카탈로그 통합으로 완전한 주문 정보 구성
- **금융기관**: 고객 데이터와 거래 내역 매칭으로 360도 고객 뷰 생성
- **제조업체**: 공급망 데이터와 품질 데이터 통합으로 품질 추적 시스템 구축

## 📝 관련 문제

**Question:** A retail corporation wishes to connect consumer orders with data from its product catalog's product descriptions. Each dataset contains records with a unique structure and format. A data analyst attempted to integrate the datasets using a spreadsheet, however the effort resulted in duplicate entries and records that were not mixed correctly. The business needs a solution for combining related records from the two databases and removing duplicates. Which solution will satisfy these criteria?

**Options:**

- A) Use an AWS Lambda function to process the data. Use two arrays to compare equal strings in the fields from the two datasets and remove any duplicates.
- B) Create AWS Glue crawlers for reading and populating the AWS Glue Data Catalog. Call the AWS Glue SearchTables API operation to perform a fuzzy-matching search on the two datasets, and cleanse the data accordingly.
- C) Create AWS Glue crawlers for reading and populating the AWS Glue Data Catalog. Use the FindMatches transform to cleanse the data.
- D) Create an AWS Lake Formation custom transform. Run a transformation for matching products from the Lake Formation console to cleanse the data automatically.

**정답: C) AWS Glue crawlers + FindMatches transform**

💡 추가 설명:

- **옵션 A** - Lambda는 대용량 데이터 처리에 부적합하며, 단순 문자열 비교로는 서로 다른 구조의 데이터를 효과적으로 매칭하기 어려움
- **옵션 B** - SearchTables API는 Data Catalog 내 테이블을 검색하는 용도이지, 실제 데이터 레코드 매칭이나 클렌징 기능을 제공하지 않음
- **옵션 D** - Lake Formation은 데이터 레이크 거버넌스에 특화된 서비스로, 복잡한 데이터 변환이나 매칭 기능을 제공하지 않음