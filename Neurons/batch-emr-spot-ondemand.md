---

title: batch-emr-spot-ondemand
created: 2025-08-21
modified: 2025-08-21
tags:
- service/aws-batch
- service/emr
- instance/spot-instances
- instance/on-demand
- architecture/cost-optimization
aliases: ["batch-processing", "emr-nodes", "spot-pricing"]

---

# AWS Batch와 EMR에서 Spot/On-Demand Instance 적합한 활용

## 🎯 핵심 포인트

대규모 배치 처리 작업의 경우 데이터 손실 위험과 안정성을 고려하여, 중요한 관리 노드는 On-Demand Instance로 안정성을 확보하고 연산 전용 노드는 Spot Instance로 비용을 절약할 수 있다.

## 📝 설명

### AWS Batch vs EMR 아키텍처 차이점

**AWS Batch**는 단순한 배치 작업 처리에 적합하며, Compute Environment 자체가 작업 실행 인스턴스와 동일합니다. 반면 **EMR**은 분산 컴퓨팅을 위한 명확한 노드 역할 분리가 있어 더 세밀한 비용 최적화가 가능합니다.

### EMR 노드별 역할과 Instance 타입 선택

**Master Node (마스터 노드)**
- 클러스터 관리, 작업 스케줄링, HDFS NameNode 역할
- Single Point of Failure이므로 반드시 On-Demand Instance 사용
- 24시간 연속 실행이 필요한 실시간 처리에서는 절대 중단되면 안됨

**Core Node (코어 노드)**  
- HDFS 데이터 저장 + 연산 작업 병행
- 영구 데이터 보관으로 인한 데이터 손실 위험 때문에 On-Demand Instance 권장
- Spark Streaming과 같은 실시간 처리에서 체크포인트 데이터 저장

**Task Node (태스크 노드)**
- 순수 연산 작업만 수행 (Stateless)
- 데이터 저장하지 않아 종료되어도 데이터 손실 없음
- Spot Instance 사용으로 최대 90% 비용 절약 가능

### 아키텍처 플로우

```
실시간 스트림 처리 (24시간):
Data Source → Master Node (On-Demand) → 작업 분배
                ↓
Core Nodes (On-Demand) → 지속적 처리 + 데이터 저장
                ↓
Task Nodes (Spot) → 추가 연산 파워 (선택적)
                ↓
결과 저장 → HDFS (Core Nodes)
```

### Trade-offs 고려사항

**Spot Instance 장점**:
- 최대 90% 비용 절약
- 자동 스케일링으로 유연한 리소스 확장
- 연산 전용 태스크에 최적화

**Spot Instance 단점**:
- 2분 경고 후 AWS 강제 종료 가능
- 장시간 실행 작업 중 데이터 손실 위험
- 예측 불가능한 가용성

**On-Demand Instance 장점**:
- 작업 완료까지 안정적 실행 보장
- 예측 가능한 가격과 성능
- 중요한 시스템 컴포넌트에 적합

**On-Demand Instance 단점**:
- 높은 비용 (Spot 대비 최대 10배)
- 필요시에만 활성화되지만 시간당 고정 요금

## 🔍 주요개념

### Map-Reduce 작업 분류

- **Map Task**: 큰 데이터를 작은 조각으로 나누어 병렬 처리하는 매핑 작업
- **Reduce Task**: Map 결과들을 모아서 최종 집계하는 축소 작업

### Instance 타입별 제어권

- **On-Demand**: 사용자가 필요에 따라 시작/종료 제어 (내 의지로 제어)
- **Spot Instance**: AWS 용량 상황에 따라 강제 종료 가능 (AWS 사정에 따라 제어)

### 실전 적용

- **실시간 스트림 처리**: Master/Core는 On-Demand, Task는 Spot으로 24시간 안정성 확보
- **대용량 배치 분석**: 중요 데이터 보관 노드는 On-Demand, 추가 연산은 Spot 활용
- **ML 모델 훈련**: 체크포인팅 가능한 작업은 Spot, 모델 저장은 On-Demand 환경

## 📝 관련 문제

**Question:** A company wants to run large-scale batch computing workloads using AWS services. They need to ensure 24/7 real-time stream processing with cost optimization. Which architecture provides the best balance of reliability and cost-effectiveness?

**Options:**

- A) Use AWS Batch with all Spot Instances for both compute environment and job execution
- B) Use EMR with Master and Core nodes as On-Demand, Task nodes as Spot Instances  
- C) Use AWS Batch with On-Demand compute environment and Spot job execution
- D) Use EMR with all nodes as Spot Instances for maximum cost savings
- E) Use AWS Elastic Beanstalk with auto-scaling On-Demand instances

**정답: B) Use EMR with Master and Core nodes as On-Demand, Task nodes as Spot Instances**

💡 추가 설명:

- **Option A** - Spot Instance 중단시 전체 작업 데이터 손실 위험, 24시간 연속 처리 불가능
- **Option C** - AWS Batch에서는 compute environment와 job execution의 분리가 기술적으로 불가능
- **Option D** - Master 노드 중단시 전체 클러스터 장애, 실시간 처리 연속성 보장 불가
- **Option E** - Elastic Beanstalk은 웹 애플리케이션용으로 배치/스트림 처리에 부적합

---