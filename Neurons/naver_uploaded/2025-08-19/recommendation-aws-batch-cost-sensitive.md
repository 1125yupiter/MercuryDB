---
title: recommendation-aws-batch-cost-sensitive
created: 2025-08-16
modified: 2025-08-16
tags:
  - problem/recommendation
  - service/aws-batch
  - constraint/cost-sensitive
  - deployment/batch
  - method/deep-learning
aliases:
  - batch-job
  - gpu-training
  - cost-sensitive
  - keras
---

# 딥러닝 추천 시스템을 위한 AWS Batch 기반 비용 효율적 워크플로우

## 🎯 핵심 포인트

딥러닝 기반 제품 추천 시스템을 운영할 때, GPU 사용량이 많은 배치(batch) 작업의 경우 **AWS Batch**와 **스팟 인스턴스**를 사용하여 비용을 최소화하고 운영을 자동화할 수 있습니다.

-----

## 📝 설명

### AWS Batch가 딥러닝 배치 작업에 적합한 이유

기존의 딥러닝 워크플로우는 GPU 인스턴스를 수동으로 관리하며, 사용하지 않는 시간에도 비용이 발생하는 비효율적인 구조였습니다. AWS Batch는 이러한 문제를 해결하는 데 최적화된 관리형 서비스입니다.

**AWS Batch의 핵심 기능**:

1.  **동적 리소스 프로비저닝**: 작업에 필요한 컴퓨팅 리소스(GPU 인스턴스)를 자동으로 프로비저닝하고, 작업이 완료되면 종료하여 비용을 절감합니다.
2.  **중앙 집중식 큐**: 모든 작업을 중앙 큐에 제출하여 체계적으로 관리하고, 우선순위에 따라 순차적으로 실행할 수 있습니다.
3.  **자동 재시작**: 장시간 실행되는 딥러닝 작업의 경우, 네트워크 오류나 스팟 인스턴스 회수(reclaim)로 인해 작업이 중단되더라도 자동으로 재시작하여 안정성을 보장합니다.

### 아키텍처 플로우

```
[S3 버킷: 데이터셋]
    ↓
[AWS EventBridge] --- (매주 월/금) --> [AWS Batch Job Submission]
    ↓
[AWS Batch Job Queue]
    ↓
[AWS Batch Compute Environment] --- (GPU Spot Instances)
    ↓
[Containerized Job] --- (Keras 모델 학습)
    ↓
[S3 버킷: 결과 저장]
```

### Trade-offs 고려사항

**AWS Batch 장점**:

  * **비용 효율성**: 스팟 인스턴스를 활용하여 EC2 온디맨드 인스턴스 대비 최대 90% 비용 절감 효과를 볼 수 있습니다.
  * **운영 자동화**: 인스턴스 관리, 작업 큐, 스케줄링, 실패 시 재시작 등을 모두 자동화하여 운영 오버헤드를 최소화합니다.
  * **탄력적 확장성**: 작업 요구사항에 따라 컴퓨팅 리소스를 자동으로 확장/축소하여 효율성을 극대화합니다.

**AWS Batch 단점**:

  * **배치 작업에 특화**: 실시간(real-time) 또는 낮은 지연 시간(low-latency)이 요구되는 추론 작업에는 부적합합니다.
  * **초기 설정**: 컨테이너 이미지 생성, 작업 정의(Job Definition) 및 컴퓨팅 환경 설정 등 초기 구성 작업이 필요합니다.

**AWS Fargate 장점**:

  * **완전한 서버리스**: 사용자가 서버나 클러스터를 전혀 관리할 필요가 없습니다.
  * **간편한 사용**: 컨테이너 실행에 최적화되어 있어 배포가 매우 간단합니다.

**AWS Fargate 단점**:

  * **GPU 미지원**: 딥러닝 모델 학습에 필수적인 GPU 인스턴스 유형을 지원하지 않습니다. 이 때문에 본 사용 사례에 적합하지 않습니다.

-----

## 🔍 주요개념

### GPU 인스턴스 vs. 서버리스

  * **GPU 인스턴스 (EC2 기반)**: 사용자가 직접 운영하는 가상 서버로, 하드웨어(GPU)에 대한 완벽한 제어권을 가집니다. 하지만 인스턴스가 켜져 있는 동안 지속적으로 비용이 발생하며, 리소스 관리가 필요합니다.
  * **서버리스 (Fargate)**: 서버 관리 부담을 AWS에 위임하는 방식입니다. 컨테이너가 실행되는 동안만 비용이 청구되지만, 현재 GPU를 지원하지 않아 딥러닝 학습과 같은 고성능 컴퓨팅 작업에는 사용할 수 없습니다.

### 실전 적용

  * **비즈니스 시나리오 1: 야간 배치 학습**: 매일 밤이나 주말에 대량의 데이터를 사용하여 딥러닝 모델을 학습시키는 경우.
  * **비즈니스 시나리오 2: 대규모 시뮬레이션**: 복잡한 물리 시뮬레이션이나 금융 모델링을 병렬로 처리하는 경우.
  * **비즈니스 시나리오 3: 유전체 분석**: 대용량 유전체 데이터를 처리하여 특정 유전자 변이를 찾아내는 경우.

-----

## 📝 관련 문제

**Question:** A company runs deep neural networks on GPU-based instances for a product recommendation system. The workflow, which runs once a week from Monday to Friday, fetches data from an S3 bucket and trains a Keras model from a local repository. The results are continuously stored in a separate bucket. The current manual process is expensive. The ML team seeks an automated, cost-effective solution with a central queue and automatic job retries in case of failure. How should the team redesign the workflow?

**Options:**

  - A) Run the workflow in ECS clusters of EC2 Spot Instances and create a scheduled task using cron expressions.
  - B) Implement the workflow in an AWS Deep Learning Container managed by AWS Fargate. Use Spot Instances and create a scheduled task.
  - C) Run the workflow in cheaper GPU-based EC2 instances and schedule the task using AWS Instance Scheduler.
  - D) Implement the workflow as containerized jobs running on GPU-based Spot Instances in AWS Batch.

**정답: D) Implement the workflow as containerized jobs running on GPU-based Spot Instances in AWS Batch.**

💡 추가 설명:

  * **오답 옵션 A**: ECS는 중앙 큐나 자동 재시작 기능을 기본으로 제공하지 않으며, 클러스터 규모를 수동으로 조정해야 합니다.
  * **오답 옵션 B**: AWS Fargate는 GPU 인스턴스를 지원하지 않아 딥러닝 학습에 부적합합니다.
  * **오답 옵션 C**: AWS Instance Scheduler는 인스턴스 시작/중지 기능만 제공할 뿐, 작업 큐나 자동 재시작 기능을 포함하지 않아 운영 오버헤드가 여전히 큽니다.