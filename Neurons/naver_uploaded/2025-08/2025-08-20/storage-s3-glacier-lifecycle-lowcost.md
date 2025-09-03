---
title: storage-s3-glacier-lifecycle-lowcost.md
created: 2025-08-17
modified: 2025-08-17
tags:
- service/s3
- service/glacier
- constraint/cost-sensitive
- constraint/compliance
- usecase/ml-pipeline
aliases: ["s3 lifecycle", "glacier deep archive", "cold storage", "s3 cost optimization"]
---

# 📦 Amazon S3 + Glacier 기반 머신러닝 데이터 보관 전략

## 🎯 핵심 포인트
머신러닝 파이프라인에서 **즉시 접근이 필요한 가공 데이터**는 S3 Standard에 저장하고, **드물게 접근되는 미가공 데이터**는 Glacier Deep Archive에 보관하면, 고가용성과 규제 요구사항을 만족하면서 비용을 최소화할 수 있다.

## 📝 설명

### Amazon S3 + Glacier가 ML 데이터 보관에 적합한 이유
- **S3 Standard**: 높은 내구성과 다중 AZ 기반 고가용성 제공 → 6개월간 가공 데이터 즉시 접근 보장  
- **S3 Lifecycle Rules**: 정의된 기간 이후 자동으로 더 저렴한 스토리지 클래스로 전환 가능  
- **Glacier Deep Archive**: 장기 보관용 초저가 스토리지, 12시간 이내 복구 지원 → 연 1~2회 감사에도 충분  

이 조합을 통해 **즉시 접근성과 비용 절감**을 동시에 달성할 수 있다.

### 아키텍처 플로우

[Processed Data] --> [S3 Standard] --(180 days lifecycle)--> [S3 Glacier]
[Unprocessed Data] --> [S3 Glacier Deep Archive] --(On Demand Retrieval)--> [Audit Access]


### Trade-offs 고려사항

**S3 Standard 장점**:
- 고가용성 (멀티 AZ)
- 즉시 접근 가능
- 라이프사이클 전환 유연성

**S3 Standard 단점**:
- 장기 보관 시 비용 부담

**Glacier Deep Archive 장점**:
- 최저 비용 스토리지
- 규제 준수 장기 보관 최적화

**Glacier Deep Archive 단점**:
- 복구 지연(최대 12시간)
- 빈번 조회에는 부적합

**EBS/One-Zone-IA와 비교**:
- EBS: 고성능이지만 객체 스토리지 대비 고비용
- One-Zone-IA: 단일 AZ라 가용성 요건 위반

## 🔍 주요개념

### S3 Standard vs S3 Glacier Deep Archive
- **S3 Standard**: 고가용성, 즉시 접근, 빈번한 조회 데이터에 적합
- **Glacier Deep Archive**: 최저 비용, 드물게 접근, 장기 규제 보관용

### 실전 적용
- **Processed Data**: 모델 학습 결과물, 6개월 동안 즉시 조회 가능해야 하는 경우  
- **Unprocessed Data**: 원본 로그/트랜잭션, 감사를 위해만 필요  
- **Lifecycle Policy**: 시간 기반으로 자동 전환, 운영 간소화 및 비용 최적화  

## 📝 관련 문제

**Question:**  
A Data Scientist is designing a scalable data repository as part of a machine learning pipeline. Requirements:  
- Processed data: immediate access, highly available, store ≥6 months  
- Unprocessed data: rarely accessed, must be retrievable within 15 hours for annual/bi-annual audit  
What is the MOST cost-effective storage configuration?

**Options:**
- A) Store processed data in Amazon EBS gp2, then move to sc1; unprocessed data in Glacier Deep Archive  
- B) Store both processed & unprocessed data in S3 Standard, move all to Glacier Deep Archive after 6 months  
- C) Store processed data in S3 One Zone-IA, unprocessed data in Glacier Deep Archive  
- D) Store processed data in S3 Standard, move to Glacier after 6 months; unprocessed data in Glacier Deep Archive  

**정답: D) Amazon S3 Standard + Lifecycle to Glacier, Glacier Deep Archive for raw data**

💡 추가 설명:
- **Option A**: EBS 비용 과다, 객체 스토리지에 비해 비효율적  
- **Option B**: Unprocessed data는 처음부터 Deep Archive에 저장하는 편이 비용 절감  
- **Option C**: One Zone-IA는 고가용성 요건 위반  
- **Option D**: 고가용성과 비용 절감을 모두 충족하는 최적 조합  
