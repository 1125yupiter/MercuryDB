---
title: emr-spot-task-nodes
created: 2025-08-16
modified: 2025-08-16
tags:
- service/emr
- deployment/spot-instances
- constraint/cost-optimization
- architecture/task-nodes
- usecase/long-running-tasks
aliases: ["emr-spot", "task-node-spot", "emr-cost-optimization"]

---

# EMR Task Nodes에서 Spot 인스턴스를 활용한 비용 최적화

## 🎯 핵심 포인트

Critical long-running task를 EMR 클러스터에서 실행할 경우 Task nodes에서 Spot 인스턴스를 사용하면, 데이터 손실 없이 최대 90%까지 비용을 절감할 수 있다.

## 📝 설명

### EMR Task Nodes가 Spot 인스턴스에 적합한 이유

Task nodes는 데이터 처리만 담당하고 HDFS에 영구 데이터를 저장하지 않는 특성을 가지고 있습니다. 이러한 특징으로 인해 Spot 인스턴스가 중단되어도 데이터 손실이 발생하지 않으며, 클러스터 전체에 미치는 영향이 최소화됩니다. 특히 완전히 트래픽이 없을 때 자동으로 종료되어 비용 절감 효과를 극대화할 수 있습니다.

### 아키텍처 플로우

```
Master Node (On-Demand)
    ↓ (클러스터 관리)
Core Nodes x5 (On-Demand)
    ↓ (HDFS 데이터 저장 + 처리)
Task Nodes x10 (Spot Instances)
    ↓ (데이터 처리만 담당)
비용 절감 + 안정성 확보
```

### Trade-offs 고려사항

**Task Nodes Spot 장점**:
- 데이터 손실 없음 (HDFS 데이터 저장하지 않음)
- 최대 90% 비용 절감
- 클러스터 안정성 유지
- 동적 스케일링 가능

**Task Nodes Spot 단점**:
- 처리 속도 일시적 감소 가능성
- Spot 가격 변동에 따른 가용성 변화

**Core Nodes Spot 장점**:
- 더 많은 인스턴스에 Spot 적용 가능

**Core Nodes Spot 단점**:
- HDFS 데이터 손실 위험
- 클러스터 전체 안정성 저하
- Long-running task에서 재시작 비용 발생

## 🔍 주요개념

### EMR 노드 타입 비교

- **Master Node**: 클러스터 관리 및 메타데이터 담당, 단일 장애점
- **Core Node**: HDFS 데이터 저장 + 데이터 처리, 영구 저장소 역할  
- **Task Node**: 데이터 처리만 담당, 임시 컴퓨팅 리소스

### 실전 적용

- **배치 데이터 처리**: 야간 ETL 작업에서 Task nodes를 Spot으로 운영하여 비용 절감
- **머신러닝 훈련**: 대용량 데이터셋 전처리 시 Task nodes로 병렬 처리 확장
- **로그 분석**: 실시간이 아닌 주기적 로그 분석에서 유연한 리소스 확장

## 📝 관련 문제

**Question:** A Data Engineer is implementing a critical long-running task in an Amazon EMR cluster. After estimating its workload, the Engineer has decided to configure the cluster with a single master node, 5 core nodes, and 10 task nodes. He plans to use Spot instances to further reduce costs. Which node should the Engineer choose to launch the Spot instances?

**Options:**

- A) Master Node
- B) Core Nodes  
- C) Task Nodes
- D) Both Core and Task Nodes
- E) Either Master or Core Nodes

**정답: C) Task Nodes**

💡 추가 설명:

- **Master Node** - 클러스터 관리 담당으로 중단 시 전체 클러스터 실패
- **Core Nodes** - HDFS 데이터 저장으로 중단 시 데이터 손실 및 복구 비용 발생  
- **Both Core and Task Nodes** - Core node 포함으로 데이터 손실 위험
- **Either Master or Core Nodes** - 두 옵션 모두 critical long-running task에 부적합