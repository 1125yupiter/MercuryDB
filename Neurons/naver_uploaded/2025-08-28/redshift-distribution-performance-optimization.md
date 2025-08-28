---

title: redshift-distribution-performance-optimization
created: 2025-08-19
modified: 2025-08-19
tags:
- service/redshift
- performance/distribution
- architecture/data-warehouse
- optimization/workload-balancing
- concept/slice-management
aliases: ["redshift-distribution", "redshift-slice", "data-distribution"]

---

# Amazon Redshift에서 워크로드 균등 분산을 위한 Distribution Style 선택

## 🎯 핵심 포인트

다중 노드 Redshift 클러스터에서 워크로드를 모든 노드와 슬라이스에 균등하게 분산해야 하는 경우, ROUND ROBIN distribution style을 사용할 수 있다.

## 📝 설명

### ROUND ROBIN Distribution이 워크로드 균등 분산에 적합한 이유

ROUND ROBIN distribution은 테이블의 행들을 클러스터 내 모든 슬라이스에 균등하게 순차적으로 분배하는 방식입니다. 이는 데이터의 내용이나 특정 컬럼 값에 관계없이 각 슬라이스가 동일한 양의 데이터를 받게 되어, 모든 노드와 슬라이스가 동일한 워크로드를 처리하게 됩니다.

### 아키텍처 플로우

```
Redshift 클러스터 (4노드 × 2슬라이스 = 8슬라이스)
├── 노드 1 [슬라이스 0, 1] ← Row 1, 9, 17, 25...
├── 노드 2 [슬라이스 2, 3] ← Row 2, 10, 18, 26...
├── 노드 3 [슬라이스 4, 5] ← Row 3, 11, 19, 27...
└── 노드 4 [슬라이스 6, 7] ← Row 4, 12, 20, 28...

ROUND ROBIN 분산 → 각 슬라이스별 동일한 데이터량
→ 균등한 워크로드 분산 → 최대 병렬 처리 성능
```

### Trade-offs 고려사항

**ROUND ROBIN 장점**:
- 완벽한 데이터 균등 분산
- 모든 노드의 CPU/메모리 자원 균등 활용
- Data skew 문제 완전 해결
- 예측 가능한 성능

**ROUND ROBIN 단점**:
- JOIN 연산 시 네트워크 트래픽 증가
- 특정 컬럼 기반 필터링 시 모든 노드 스캔 필요
- 관련 데이터 co-location 불가능

**KEY Distribution 장점**:
- JOIN 성능 최적화 (같은 키값이 동일 노드에 저장)
- 네트워크 트래픽 감소
- 관련 데이터 locality 향상

**KEY Distribution 단점**:
- Data skew 위험 (특정 노드에 데이터 집중)
- 불균등한 워크로드 분산 가능성
- 키 선택에 따른 성능 차이 발생

**ALL Distribution 장점**:
- 모든 JOIN이 로컬에서 실행
- 작은 dimension 테이블에 효과적

**ALL Distribution 단점**:
- 스토리지 요구량 급증 (노드 수만큼 복제)
- 대용량 테이블에는 부적절
- INSERT/UPDATE 성능 저하

## 🔍 주요개념

### 슬라이스(Slice)와 노드 관계

- **슬라이스**: 각 노드 내의 독립적인 데이터 처리 단위 (논리적 파티션)
- **노드**: 물리적 컴퓨팅 리소스 (CPU, 메모리, 스토리지)
- **병렬 처리**: 각 슬라이스가 독립적으로 쿼리 실행

### Distribution Style 비교

- **ROUND ROBIN**: 순차적 균등 분산, 워크로드 밸런싱 최적화
- **KEY**: 특정 컬럼 기반 분산, JOIN 성능 최적화
- **ALL**: 전체 테이블 복제, 작은 테이블용
- **AUTO**: Redshift가 자동 선택 (기본값)

### 실전 적용

- **대용량 fact 테이블**: ROUND ROBIN으로 균등 분산
- **JOIN이 빈번한 테이블들**: KEY distribution으로 co-location
- **작은 dimension 테이블**: ALL distribution으로 로컬 JOIN
- **분석용 임시 테이블**: AUTO로 자동 최적화

## 📝 관련 문제

**Question:** A company is using Amazon Redshift for their data warehousing needs. They have created a new Redshift cluster with 4 nodes and 2 slices per node. They want to ensure that their workload is spread evenly across all nodes and slices to achieve maximum performance. Which of the following options should they use to achieve this goal?

**Options:**

- A) Create a distribution style of ALL on their tables
- B) Use compound sorting on their tables  
- C) Create a distribution style of KEY on their tables
- D) Use interleaved sorting on their tables
- E) Use a ROUND ROBIN distribution style on their tables

**정답: E) Use a ROUND ROBIN distribution style on their tables**

💡 추가 설명:

- **ALL distribution** - 전체 테이블을 모든 노드에 복제하여 스토리지 낭비 및 INSERT/UPDATE 성능 저하 발생
- **Compound/Interleaved sorting** - 테이블 정렬 방식으로 노드 간 데이터 분산과는 무관한 기능
- **KEY distribution** - 특정 컬럼 값 기준으로 분산하여 data skew 발생 가능, 불균등한 워크로드 분산 위험