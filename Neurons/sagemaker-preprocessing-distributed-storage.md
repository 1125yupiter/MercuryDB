---
title: sagemaker-preprocessing-distributed-storage
created: 2025-08-18
modified: 2025-08-18
tags:
- service/sagemaker-processing
- storage/efs
- storage/fsx-lustre
- constraint/high-throughput
- deployment/distributed
aliases: ["ml-data-pipeline", "distributed-preprocessing", "posix-storage"]
---

# SageMaker Processing을 위한 분산 데이터 전처리 스토리지 솔루션

## 🎯 핵심 포인트

대용량 머신러닝 데이터를 여러 인스턴스에서 병렬로 전처리하는 경우 POSIX 호환 파일 시스템에서, Amazon EFS와 FSx for Lustre를 조합하여 높은 처리량과 비용 효율성을 동시에 달성할 수 있다.

## 📝 설명

### Amazon EFS와 FSx for Lustre가 분산 ML 전처리에 적합한 이유

**Amazon EFS (Elastic File System)**는 완전관리형 NFS 파일 시스템으로 여러 SageMaker Processing 컨테이너에서 동시에 마운트할 수 있습니다. POSIX 호환성을 제공하며 자동으로 페타바이트급까지 확장되고, 사용량 기반 요금제와 Intelligent-Tiering을 통해 비용을 최적화할 수 있습니다.

**Amazon FSx for Lustre**는 고성능 병렬 파일 시스템으로 HPC와 ML 워크로드에 특화되었습니다. S3와의 네이티브 통합을 통해 데이터를 투명하게 파일 시스템으로 노출하며, 수천 개의 컴퓨트 노드에서 동시 접근 시 일관된 높은 성능을 제공합니다.

### 아키텍처 플로우

```
S3 원본 데이터 (20TB)
      ↓
FSx for Lustre ←→ S3 자동 동기화
      ↓
SageMaker Processing Job
      ↓
여러 Processing 인스턴스 (병렬)
      ↓
공유 파일 시스템 접근 (POSIX)
      ↓
전처리된 데이터 → S3/EFS 저장
```

### Trade-offs 고려사항

**Amazon EFS 장점**:
- 완전관리형으로 운영 오버헤드 최소화
- 자동 확장 및 내구성 보장
- 표준 POSIX 인터페이스 지원
- IA 스토리지 클래스로 비용 절약

**Amazon EFS 단점**:
- General Purpose 모드에서 제한된 IOPS
- 네트워크 기반으로 인한 지연시간

**Amazon FSx for Lustre 장점**:
- 초고성능 I/O (GB/s 수준)
- S3와 seamless 통합
- ML/HPC 워크로드 최적화
- 병렬 처리에 최적화된 아키텍처

**Amazon FSx for Lustre 단점**:
- EFS 대비 높은 비용
- 구성 및 관리 복잡성 증가

**Amazon EBS 단점**:
- 단일 인스턴스만 접근 가능 (공유 불가)
- 분산 처리에 부적합

**Amazon S3 단점**:
- POSIX 파일 시스템 미지원
- REST API 기반으로 직접 파일 접근 불가
- 높은 지연시간 (I/O 집약적 작업에 부적합)

## 🔍 주요개념

### 스토리지 유형 비교

- **블록 스토리지 (EBS)**: 단일 인스턴스 전용, 높은 IOPS, 공유 불가
- **파일 스토리지 (EFS/FSx)**: 여러 인스턴스 공유, POSIX 호환, 네트워크 기반
- **객체 스토리지 (S3)**: 무제한 확장, REST API, 파일 시스템 미지원

### 실전 적용

- **대용량 이미지/비디오 전처리**: FSx for Lustre로 높은 대역폭 활용
- **텍스트 데이터 분산 처리**: EFS로 비용 효율적 공유 스토리지 구현  
- **실시간 피처 엔지니어링**: 두 시스템 조합으로 성능과 비용 최적화

## 📝 관련 문제

**Question:** You are building a machine learning data pipeline that performs distributed data preprocessing on large volumes of training data using Amazon SageMaker Processing jobs. The preprocessing script is I/O-intensive, needs high throughput access to a shared dataset (~20 TB) via a POSIX-compliant file system, and runs across multiple instances in parallel. The solution should minimize operational overhead and be cost-effective. Which storage options are most appropriate? (Select two)

**Options:**
- A) Amazon EBS
- B) Amazon S3  
- C) AWS Glue Data Catalog
- D) Amazon EFS
- E) Amazon FSx for Lustre

**정답: D) Amazon EFS, E) Amazon FSx for Lustre**

💡 추가 설명:
- **Amazon EBS** - 단일 인스턴스 전용 블록 스토리지로 분산 환경에서 공유 불가
- **Amazon S3** - 객체 스토리지로 POSIX 파일 시스템 인터페이스 미지원
- **AWS Glue Data Catalog** - 메타데이터 저장소이며 실제 데이터 스토리지가 아님