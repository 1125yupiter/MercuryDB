---

title: emr-spark-spot-seasonal
created: 2025-08-19
modified: 2025-08-19
tags:
- service/emr
- compute/spot-instances
- constraint/seasonal-workload
- storage/hdfs
- processing/spark
aliases: ["EMR", "spot", "seasonal"]

---

# Amazon EMR에서 Spark 워크로드를 위한 Spot 인스턴스 활용

## 🎯 핵심 포인트

계절성 변동이 있는 대용량 데이터 처리 워크로드의 경우 EMR에서, Task 노드에만 Spot 인스턴스를 사용하여 데이터 손실 없이 비용을 최적화할 수 있다.

## 📝 설명

### Amazon EMR이 계절성 데이터 처리에 적합한 이유

Amazon EMR은 Hadoop과 Spark를 위한 관리형 클러스터 서비스로, 노드 유형별로 다른 역할을 수행합니다. 이커머스 환경에서 휴일이나 이벤트 시 급증하는 데이터 처리 요구사항에 대해 노드별 특성을 활용한 비용 최적화가 가능합니다. Task 노드는 HDFS 데이터를 저장하지 않고 오직 데이터 처리만 담당하므로, Spot 인스턴스 중단 시에도 데이터 손실 위험 없이 워크로드를 재분산할 수 있습니다.

### 아키텍처 플로우

```
데이터 수집 → HDFS 저장 (Core 노드) → Spark 작업 분배
    ↓
Master 노드 (클러스터 관리)
    ↓
Core 노드 (HDFS + 일부 처리) + Task 노드 (처리 전용, Spot)
    ↓
결과 저장 → S3/데이터베이스
```

### Trade-offs 고려사항

**Task 노드 Spot 인스턴스 장점**:
- 최대 90% 비용 절감
- 자동 스케일링으로 계절성 변동 대응
- 데이터 손실 위험 없음
- 처리 능력 확장에 최적화

**Task 노드 Spot 인스턴스 단점**:
- 예측 불가능한 중단 발생
- 작업 완료 시간 지연 가능성
- 복잡한 모니터링 필요

**Core/Master 노드 On-Demand/Reserved 장점**:
- 안정적인 HDFS 데이터 보호
- 클러스터 가용성 보장
- 예측 가능한 성능

**Core/Master 노드 On-Demand/Reserved 단점**:
- 높은 비용
- 유연성 제한

## 🔍 주요개념

### EMR 노드 타입 비교

- **Master Node**: 클러스터 전체 조정 및 관리, 중단 시 전체 클러스터 종료
- **Core Node**: HDFS 데이터 저장 + Spark 작업 실행, 중단 시 데이터 손실 위험
- **Task Node**: 데이터 처리만 담당, HDFS 미사용으로 중단 허용 가능

### 실전 적용

- 이커머스 Black Friday/Cyber Monday 트래픽 급증 처리
- 소매업체 연말 재고 분석 및 예측 모델링
- 미디어 회사 시즌별 컨텐츠 소비 패턴 분석

## 📝 관련 문제

**Question:** An e-commerce company processes substantial volumes of consumer behavior data stored in HDFS using Apache Spark on Amazon EMR. Daily processing shows significant seasonal fluctuations, with notable spikes during holidays and major sales events. What is the most cost-effective strategy to manage variable demands without risking data loss or requiring cluster termination?

**Options:**

- A) Deploy EC2 Spot instances for both core and task nodes, reserving EC2 Reserved instances for the master node only
- B) Allocate reserved instances for task nodes, and employ Spot instances for core nodes
- C) Implement EC2 Spot instances across all node types, including master, core, and task nodes
- D) Utilize EC2 Spot instances for Spark task nodes exclusively, while employing other instance types for core and master nodes

**정답: D) Utilize EC2 Spot instances for Spark task nodes exclusively, while employing other instance types for core and master nodes**

💡 추가 설명:

- **Option A** - Core 노드 Spot 인스턴스 사용 시 HDFS 데이터 손실 위험 발생
- **Option B** - Task 노드에 Reserved 인스턴스는 비용 비효율적이며 유연성 저하
- **Option C** - Master 노드 중단 시 전체 클러스터 종료, Core 노드 중단 시 데이터 손실
- **정답 D** - Task 노드만 Spot으로 비용 절감하면서 데이터 보호와 클러스터 안정성 확보