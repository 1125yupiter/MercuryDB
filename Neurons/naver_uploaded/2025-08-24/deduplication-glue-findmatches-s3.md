---

title: deduplication-glue-findmatches-s3
created: 2025-08-18
modified: 2025-08-18
tags:
- service/glue
- technique/findmatches
- constraint/minimal-effort
- usecase/data-lake
- problem/deduplication
aliases: ["glue-dedup", "findmatches", "ml-transform"]

---

# S3 데이터 레이크에서 AWS Glue FindMatches를 활용한 효과적인 중복 제거

## 🎯 핵심 포인트

출판사가 여러 소스에서 수집한 책 데이터의 중복을 제거해야 하는 경우 S3 데이터 레이크에서, AWS Glue FindMatches ML Transform을 활용해 최소한의 개발 및 유지보수 노력으로 지능적인 중복 제거를 수행할 수 있다.

## 📝 설명

### AWS Glue FindMatches가 중복 제거에 적합한 이유

AWS Glue FindMatches ML Transform은 머신러닝을 기반으로 한 서버리스 데이터 중복 제거 솔루션입니다. 이 서비스는 단순한 완전 일치 방식을 넘어서 유사성 기반 중복 감지를 통해 더욱 정교한 데이터 정제를 제공합니다.

핵심 특징:
- **머신러닝 기반 매칭**: 완전히 동일하지 않더라도 유사한 레코드를 식별
- **서버리스 아키텍처**: 인프라 관리 부담 없음
- **GUI 기반 설정**: 최소한의 코딩으로 구성 가능
- **자동화된 처리**: 실시간 또는 배치 처리 지원
- **확장성**: 대량 데이터 처리 가능

### 아키텍처 플로우

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────────────┐    ┌──────────────────┐    ┌─────────────────────┐
│   다중 데이터     │──→│  S3 Raw Data     │──→│ Glue FindMatches       │──→│ S3 Clean Data    │──→│   후속 데이터       │
│     소스        │    │     Bucket       │    │   ML Transform         │    │     Lake         │    │     처리           │
└─────────────────┘    └──────────────────┘    └─────────────────────────┘    └──────────────────┘    └─────────────────────┘
         │                       │                          │                           │                         │
         ↓                       ↓                          ↓                           ↓                         ↓

   • 퍼블리셔 A             • 원본 데이터 저장          • 중복 감지 및 제거          • 정제된 데이터 저장         • 분석/ML 워크로드
   • 퍼블리셔 B             • 스키마 추론              • ML 기반 매칭             • 품질 향상된 데이터         • 비즈니스 인텔리전스
   • 도서관 DB              • 파티셔닝                 • 유사성 점수 기반          • 일관된 포맷              • 보고서 생성
```

### 세부 처리 흐름

```
[데이터 수집]              [중복 제거 처리]                [데이터 출력]
─────────────────         ─────────────────────          ─────────────────

다양한 소스 → S3 저장      ML 모델 학습 → 유사성 분석      정제된 데이터 → 분석 워크로드
     │             │           │             │                 │            │
     ↓             ↓           ↓             ↓                 ↓            ↓
 원본 데이터    파티셔닝    샘플 라벨링    중복 그룹화      품질 검증     비즈니스 활용
```
### Trade-offs 고려사항

**AWS Glue FindMatches 장점**:
- 서버리스 환경으로 운영 복잡도 최소화
- 머신러닝 기반 지능적 중복 감지
- GUI를 통한 직관적 설정 및 관리
- 자동 스케일링으로 대용량 데이터 처리
- 다양한 데이터 포맷 지원

**AWS Glue FindMatches 단점**:
- 초기 모델 학습을 위한 샘플 데이터 라벨링 필요
- ML Transform 실행 시간이 대용량 데이터에서 길어질 수 있음
- 완전한 커스터마이징에는 제약

**Amazon EMR + Spark 장점**:
- 복잡한 데이터 처리 로직 구현 가능
- 다양한 알고리즘과 라이브러리 활용
- 세밀한 성능 튜닝 가능

**Amazon EMR + Spark 단점**:
- 클러스터 관리 및 모니터링 부담
- 개발 및 유지보수 복잡도 증가
- 인프라 비용 지속 발생

**Amazon Redshift 장점**:
- 강력한 SQL 기반 데이터 처리
- 고성능 분석 쿼리 지원

**Amazon Redshift 단점**:
- 데이터 웨어하우스 설정 및 관리 복잡
- 추가 데이터 이동 및 스토리지 비용
- 중복 제거 전용 솔루션이 아님

## 🔍 주요개념

### 중복 제거 방식 비교

- **완전 일치 (Exact Match)**: 모든 필드가 정확히 동일한 레코드만 중복으로 판단
- **퍼지 매칭 (Fuzzy Matching)**: 유사성 점수를 기반으로 유사한 레코드를 중복으로 판단
- **ML 기반 매칭**: 학습된 모델을 통해 복잡한 패턴의 중복 레코드 식별

### 실전 적용

- **출판업계 도서 데이터**: 여러 유통업체에서 수집한 도서 정보의 ISBN, 제목, 저자 기반 중복 제거
- **고객 데이터 통합**: 다양한 채널에서 수집된 고객 정보의 이름, 주소, 연락처 기반 중복 제거
- **제품 카탈로그 관리**: 복수 공급업체의 제품 정보를 통합하여 브랜드명, 모델명 기반 중복 제거

## 📝 관련 문제

**Question:** A publishing company is currently integrating a data feed into Amazon S3, which consists of book features originating from multiple sources. Given the redundancy of data across these sources, it is imperative to remove duplicates in the S3 data lake before proceeding with further data processing. Which approach ensures the elimination of duplicate records with the least amount of development and maintenance effort?

**Options:**

- A) Periodically process the data using a Spark DataFrame on Amazon EMR, applying the `dropDuplicates()` function to cleanse the dataset of duplicates before further processing
- B) Transfer the data into Amazon Redshift, employing a unique primary key constraint to automatically prevent duplicate entries
- C) Deploy a Glue Crawler to discern and eliminate duplicate records during its schema inference process
- D) Utilize AWS Glue's FindMatches ML Transform to detect and remove duplicate records upon their arrival

**정답: D) AWS Glue's FindMatches ML Transform**

💡 추가 설명:

- **Option A (Amazon EMR + Spark)** - EMR 클러스터 관리와 지속적인 모니터링이 필요하여 운영 복잡도가 높음
- **Option B (Amazon Redshift)** - 데이터 웨어하우스 설정과 추가적인 데이터 이동이 필요하여 복잡하고 비효율적
- **Option C (Glue Crawler)** - 스키마 추론 전용 도구로 데이터 중복 제거 기능을 제공하지 않음