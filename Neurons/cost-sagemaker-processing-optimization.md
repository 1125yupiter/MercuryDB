---

title: cost-sagemaker-processing-optimization
created: 2025-08-22
modified: 2025-08-22
tags:
- service/sagemaker-processing
- constraint/cost-sensitive
- deployment/mixed-workload
- instance/reserved-vs-ondemand
- optimization/workload-separation
aliases: ["sagemaker-cost", "ri-optimization", "processing-cost"]

---

# AWS SageMaker 비용 최적화 - 혼합 워크로드 환경에서 적합한 인스턴스 선택

## 🎯 핵심 포인트

서로 다른 리소스 요구사항을 가진 ML 워크로드가 혼재한 경우 SageMaker Processing과 워크로드 분리를 통해, 비용을 최대 75%까지 절감할 수 있다.

## 📝 설명

### SageMaker Processing이 혼합 워크로드에 적합한 이유

SageMaker Processing은 온디맨드 방식으로 데이터 처리 작업을 실행하는 관리형 서비스입니다. 특히 일시적이고 리소스 집약적인 작업에 최적화되어 있으며, 필요한 시간만큼만 인스턴스를 사용하므로 비용 효율성이 극대화됩니다.

**핵심 특징:**
- 작업 시작 시 자동으로 인스턴스 프로비저닝
- 작업 완료 후 자동으로 인스턴스 종료
- 다양한 인스턴스 타입 선택 가능 (메모리 최적화, 범용 등)
- 병렬 처리 및 자동 스케일링 지원

### 아키텍처 플로우

```
ML 전문가 작업 (피처 엔지니어링)
    ↓ [최소 CPU/메모리]
작은 범용 인스턴스 (ml.t3.medium)
    ↓ [필요시만 사용]
    
데이터 엔지니어 작업 (데이터 준비)
    ↓ [대용량 RAM 필요, 2시간]
SageMaker Processing + R5 인스턴스
    ↓ [작업 완료 후 자동 종료]
비용 절감 달성
```

### Trade-offs 고려사항

**SageMaker Processing 장점**:
- 온디맨드 실행으로 불필요한 비용 제거
- 자동 스케일링 및 인프라 관리
- 작업별 최적 인스턴스 타입 선택 가능
- 병렬 처리로 처리 시간 단축 가능

**SageMaker Processing 단점**:
- 초기 인스턴스 시작 시간 (2-3분 소요)
- 매번 환경 설정 필요
- 지속적인 모니터링 작업에는 부적합

**Always-On 노트북 인스턴스 장점**:
- 즉시 사용 가능
- 환경 설정 유지
- 대화형 개발 환경

**Always-On 노트북 인스턴스 단점**:
- 24시간 비용 발생
- 리소스 활용률 저하
- 스케일링 제한

## 🔍 주요개념

### Reserved Instance vs On-Demand 비교

- **Reserved Instance (RI)**: 1-3년 약정을 통해 최대 75% 할인, 지속적 사용 워크로드에 적합
- **On-Demand**: 사용한 만큼 지불, 불규칙한 사용 패턴에 적합
- **SageMaker Processing**: 작업 기반 온디맨드 실행, 일시적 워크로드에 최적

### 실전 적용

- **개발 단계**: 작은 범용 인스턴스로 피처 엔지니어링 및 실험
- **데이터 준비**: SageMaker Processing으로 메모리 집약적 작업 실행
- **모델 훈련**: 필요시 GPU 인스턴스를 Processing으로 단기 사용

## 📝 관련 문제

**Question:** A business is developing a machine-learning-based demand forecasting model. During development, an ML expert does feature engineering on a SageMaker notebook using minimal CPU and memory resources. A data engineer uses the same notebook for data preparation once daily, requiring large RAM and taking 2 hours. The data preparation doesn't benefit from GPU acceleration. All processes run normally on an ml.m5.4xlarge notebook instance. The organization receives AWS Budget warnings for exceeding monthly costs. Which solution provides the GREATEST cost savings?

**Options:**

- A) Change to a memory optimized instance with same vCPU as ml.m5.4xlarge. Stop notebook when not in use. Run both preprocessing and feature engineering on that instance.
- B) Keep same notebook instance type and size. Stop notebook when not in use. Run data preprocessing on P3 instance using SageMaker Processing.
- C) Change to smaller general purpose instance. Stop notebook when not in use. Run data preprocessing on ml.r5 instance with same memory as ml.m5.4xlarge using SageMaker Processing.
- D) Change to smaller general purpose instance. Stop notebook when not in use. Run data preprocessing on R5 instance with same memory using Reserved Instance option.

**정답: C) SageMaker Processing with R5 instance**

💡 추가 설명:

- **옵션 A** - 메모리 최적화 인스턴스는 피처 엔지니어링에 과도한 리소스로 비용 비효율
- **옵션 B** - P3 인스턴스는 GPU 기반으로 불필요하고 매우 비싸며, 데이터 준비에 GPU 가속 효과 없음
- **옵션 D** - Reserved Instance는 하루 2시간만 사용하는 워크로드에 1-3년 약정이 부적합하여 비용 낭비