---

title: emr-genomic-spark-memory
created: 2025-08-19 
modified: 2025-08-19 
tags:
- service/emr
- platform/spark
- workload/genomic
- constraint/memory-intensive
- instance/r5d
aliases: ["emr-cluster", "genomic-processing", "spark-memory"]

---

# EMR 클러스터 게노믹 데이터 처리를 위한 적합한 인스턴스 구성

## 🎯 핵심 포인트

대규모 게노믹 데이터셋을 Apache Spark로 처리하는 경우, 메모리 최적화된 R5d 인스턴스에서 충분한 RAM과 EBS 최적화를 통해 효과적인 처리를 할 수 있다.

## 📝 설명

### EMR이 게노믹 데이터 처리에 적합한 이유

Amazon EMR은 Apache Spark를 기반으로 한 관리형 빅데이터 플랫폼으로, 게노믹 데이터의 특성에 매우 적합합니다. 게노믹 데이터는 단일 파일이 수백 GB에서 TB 단위로 매우 크며, 이러한 대용량 파일들을 메모리에 로드하여 처리해야 최적의 성능을 얻을 수 있습니다. Spark의 인메모리 컴퓨팅 특성과 게노믹 데이터의 메모리 집약적 특성이 잘 결합됩니다.

### 아키텍처 플로우

```
S3 (게노믹 데이터 저장)
    ↓
EMR 클러스터 (Master + Core Nodes)
    ↓
Spark 애플리케이션 (메모리 내 처리)
    ↓ 
R5d 인스턴스 (메모리 최적화 + NVMe SSD)
    ↓
EBS 최적화 (전용 대역폭)
    ↓
처리된 결과 → S3 저장
```

### Trade-offs 고려사항

**R5d 인스턴스 장점**:
- 메모리 최적화로 대용량 게노믹 파일 처리에 최적
- NVMe SSD로 빠른 로컬 임시 저장소 제공
- 최신 세대로 우수한 성능/비용 비율
- CPU/메모리 균형잡힌 구성 (16 vCPU, 64GB)

**R5d 인스턴스 단점**:
- GPU 가속이 필요한 특수 워크로드에는 부적합
- 극도로 CPU 집약적인 작업에는 C5 시리즈가 더 적합할 수 있음

**C5 인스턴스 장점**:
- CPU 성능 최적화
- 컴퓨팅 집약적 작업에 특화

**C5 인스턴스 단점**:
- 메모리 용량 부족으로 게노믹 데이터 처리 시 스필링 발생
- 대용량 파일 로드 시 성능 병목

## 🔍 주요개념

### 게노믹 데이터 특성 비교

- **파일 크기**: 단일 FASTQ 파일 100-200GB, BAM 파일 수십-수백GB
- **메모리 요구량**: 압축 해제 시 원본의 3-4배 메모리 필요
- **I/O 패턴**: 대용량 순차 읽기와 랜덤 액세스가 혼재
- **처리 특성**: CPU와 메모리 모두 집약적이지만 메모리 병목이 더 치명적

### 실전 적용

- **Whole Genome Sequencing (WGS)**: 150GB FASTQ → 600GB 메모리 필요
- **변이 검출 파이프라인**: GATK 워크플로우에서 대용량 메모리 캐싱 활용
- **인구 유전학 연구**: 수천 개체의 VCF 파일 동시 분석

## 📝 관련 문제

**Question:** You have been tasked with creating an Amazon EMR cluster for processing large-scale genomic datasets. The data is stored in Amazon S3, and the workflow will be run using Apache Spark. Which of the following configurations is the most appropriate for this use case?

**Options:**

- A) A cluster with 1 master node and 10 core nodes, each with 8 vCPUs and 32 GB of memory, running on c5.xlarge instances with no EBS-optimized instances
- B) A cluster with 1 master node and 10 core nodes, each with 16 vCPUs and 64 GB of memory, running on r5d.2xlarge instances with EBS-optimized instances
- C) A cluster with 1 master node and 10 core nodes, each with 32 vCPUs and 128 GB of memory, running on r4.4xlarge instances with EBS-optimized instances
- D) A cluster with 1 master node and 10 core nodes, each with 4 vCPUs and 16 GB of memory, running on m4.large instances with no EBS-optimized instances
- E) A cluster with 1 master node and 10 core nodes, each with 4 vCPUs and 16 GB of memory, running on t3.small instances with no EBS-optimized instances

**정답: B) r5d.2xlarge instances with EBS-optimized instances**

💡 추가 설명:

- **옵션 A (c5.xlarge)** - CPU 최적화 타입이지만 32GB 메모리로는 대용량 게노믹 파일 처리 시 메모리 부족 발생
- **옵션 C (r4.4xlarge)** - 메모리는 충분하지만 구세대 인스턴스로 비용 효율성이 떨어지고 과도한 리소스
- **옵션 D (m4.large)** - 16GB 메모리로는 게노믹 데이터 처리에 완전히 부족하고 EBS 최적화 없음
- **옵션 E (t3.small)** - 버스터블 인스턴스로 지속적인 고성능 처리에 부적합하고 리소스 부족