---
title: efs-iops-performance-management
created: 2025-08-18
modified: 2025-08-18
tags:
- service/efs
- constraint/high-iops
- performance/throughput
- monitoring/cloudwatch
- storage/filesystem
aliases: ["EFS-IOPS", "EFS-Performance", "ElasticFileSystem"]
---

# EFS IOPS 성능 관리 - 높은 IOPS 사용률에서 효과적인 처리량 최적화

## 🎯 핵심 포인트

IOPS 사용률이 100%에 근접한 경우 EFS에서, Provisioned 모드일 때는 프로비저닝된 처리량을 증가시키고 Bursting 모드일 때는 스토리지 크기를 늘려 성능을 개선할 수 있다.

## 📝 설명

### EFS가 높은 IOPS 워크로드에 적합한 이유

Amazon EFS(Elastic File System)는 완전관리형 NFS 파일 시스템으로, 저장된 데이터 크기에 따라 자동으로 확장되며 여러 EC2 인스턴스에서 동시 접근이 가능합니다. 높은 IOPS 요구사항을 처리하기 위해 두 가지 처리량 모드를 제공하며, 각각의 특성에 따라 다른 최적화 전략을 적용할 수 있습니다.

### 아키텍처 플로우

```
EC2 Instance A ─┐
               ├─ Mount Targets ─ EFS File System ─ CloudWatch Metrics
EC2 Instance B ─┘                                 (PercentIOLimit, TotalIOTime)
                                                            │
                                                            ▼
                                                   Performance Mode Selection
                                                   ┌─────────────────────────┐
                                                   │ Bursting Throughput     │
                                                   │ - Storage size based    │
                                                   │ - 0.05 IOPS per GB     │
                                                   │ - Max 7,000 IOPS burst │
                                                   └─────────────────────────┘
                                                   ┌─────────────────────────┐
                                                   │ Provisioned Throughput  │
                                                   │ - Fixed performance     │
                                                   │ - Independent of size   │
                                                   │ - Predictable costs     │
                                                   └─────────────────────────┘
```

### Trade-offs 고려사항

**Provisioned Throughput 모드 장점**:
- 스토리지 크기와 관계없이 필요한 성능 설정 가능
- 즉각적이고 예측 가능한 성능 보장
- 워크로드 패턴에 맞는 세밀한 조정 가능
- 일정한 성능이 필요한 프로덕션 환경에 적합

**Provisioned Throughput 모드 단점**:
- 사용하지 않는 프로비저닝된 처리량에 대해서도 비용 발생
- 설정 변경이 필요할 때 수동 개입 필요
- 초기 용량 계획이 중요

**Bursting Throughput 모드 장점**:
- 사용한 만큼만 비용 지불
- 데이터 크기 증가 시 자동으로 성능 향상
- 관리 오버헤드가 적음
- 가변적인 워크로드에 적합

**Bursting Throughput 모드 단점**:
- 크레딧 고갈 시 기본 성능으로 제한
- 대용량 데이터가 없으면 높은 IOPS 달성 어려움
- 성능 예측이 상대적으로 어려움

## 🔍 주요개념

### 성능 모드 비교

- **General Purpose**: 최대 7,000 IOPS, 낮은 지연시간, 대부분 워크로드에 적합
- **Max I/O**: 7,000 IOPS 이상, 높은 지연시간, 대규모 병렬 워크로드 전용

### 실전 적용

- **웹 애플리케이션 콘텐츠 저장**: 여러 웹서버에서 동시 접근하는 정적 파일 저장 시 Bursting 모드로 시작하여 트래픽 증가에 따라 Provisioned로 전환
- **빅데이터 분석 워크로드**: 대용량 데이터셋을 여러 컴퓨팅 노드에서 동시 처리할 때 Max I/O 모드와 Provisioned 처리량 조합 사용
- **개발/테스트 환경**: 가변적인 사용 패턴을 가진 개발 환경에서는 Bursting 모드로 비용 최적화하면서 필요 시에만 성능 향상

## 📝 관련 문제

**Question:** A company is running a high-performance computing application on multiple EC2 instances that access a shared Amazon EFS file system. The CloudWatch metrics show that the PercentIOLimit is consistently reaching 98-100%, causing performance degradation. The file system is currently using the General Purpose performance mode with Bursting Throughput. What is the MOST effective immediate action to resolve this performance issue?

**Options:**

- A) Convert the EFS file system from General Purpose to Max I/O performance mode
- B) Increase the provisioned throughput of the EFS file system if it is in provisioned mode
- C) Reconfigure attached EC2 instances to use Elastic Block Store (EBS) instead of EFS
- D) Decrease the number of files stored in the EFS file system to reduce IOPS usage
- E) Add more data to the EFS file system to increase the baseline performance

**정답: B) Increase the provisioned throughput of the EFS file system if it is in provisioned mode**

💡 추가 설명:

- **Option A** - Max I/O 모드로 전환하면 더 높은 IOPS를 지원하지만 지연시간이 증가하며, 이미 IOPS 한계에 도달한 상황에서는 근본적 해결책이 되지 않음
- **Option C** - EBS로 전환하는 것은 아키텍처 전체 변경이 필요하고, 다중 인스턴스 동시 접근이라는 EFS의 핵심 장점을 포기하게 됨
- **Option D** - 파일 수 감소는 IOPS 사용률에 직접적인 영향을 주지 않으며, 실제로는 파일 작업의 특성이 더 중요함
- **Option E** - Bursting 모드에서 데이터 추가는 장기적 해결책이지만 즉각적인 효과를 제공하지 않으며, 불필요한 스토리지 비용 발생