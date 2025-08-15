---
title: ec2-instance-types-balanced-workload-selection
created: 2025-08-15
modified: 2025-08-15
tags:
- service/ec2
- instance/general-purpose
- workload/balanced
- architecture/graviton2
- optimization/cost-performance
aliases: ["EC2 instance types", "general purpose", "M시리즈", "balanced workload"]
---

# EC2 Instance Types - Balanced Workload Selection

## 🎯 핵심 포인트
컴퓨팅, 메모리, 네트워크 균형이 필요한 워크로드인 경우 EC2 M 시리즈에서, AWS Graviton2 기반 M6g로 최대 40% 향상된 가격 대비 성능을 제공할 수 있다.

## 📝 설명

### M 시리즈가 Balanced Workload에 적합한 이유
EC2에서 컴퓨팅, 메모리, 네트워크 리소스의 균형이 필요한 워크로드에는 M 시리즈(General Purpose) 인스턴스를 사용합니다. M 시리즈는 특정 리소스에 편중되지 않고 모든 리소스를 균등하게 제공하도록 설계되었습니다. AWS는 인스턴스 세대가 높을수록 더 나은 성능과 비용 효율성을 제공하며, M6g는 AWS Graviton2 프로세서 기반으로 기존 x86 기반 M5 대비 최대 40% 향상된 가격 대비 성능을 보여줍니다.

### 아키텍처 플로우
```
Balanced Workload Requirements
         ↓
    Resource Assessment
    (CPU + Memory + Network)
         ↓
    M Series Selection
         ↓
    Generation Comparison
    (M6g vs M5 vs M4)
         ↓
    Architecture Decision
    (Graviton2 vs x86)
```

### Trade-offs 고려사항

**M6g 장점**:
- 최신 세대의 뛰어난 성능
- AWS Graviton2 프로세서의 에너지 효율성
- M5 대비 최대 40% 향상된 가격 대비 성능
- 향상된 네트워크 성능

**M6g 단점**:
- ARM 아키텍처로 인한 일부 x86 전용 소프트웨어와의 호환성 이슈 가능성
- 레거시 애플리케이션 마이그레이션 시 검증 필요

**M5 장점**:
- 검증된 x86 아키텍처로 광범위한 소프트웨어 호환성
- 기존 애플리케이션과의 완벽한 호환성

**M5 단점**:
- M6g 대비 상대적으로 낮은 성능과 비용 효율성
- 구세대 아키텍처

## 🔍 주요개념

### EC2 인스턴스 패밀리별 특화 용도
- **M 시리즈**: 범용(General Purpose) - 컴퓨팅, 메모리, 네트워크 균형
- **C 시리즈**: 컴퓨팅 집약적 워크로드 (Compute Optimized)
- **R 시리즈**: 메모리 집약적 워크로드 (Memory Optimized)
- **T 시리즈**: 버스터블 성능 워크로드 (Burstable Performance)

### 실전 적용
- 웹 애플리케이션 서버에서 균등한 리소스 사용 패턴을 보이는 경우 M6g 선택
- 마이크로서비스 아키텍처에서 다양한 컴포넌트를 호스팅할 때 M 시리즈로 표준화
- 개발/테스트 환경에서 다양한 워크로드를 실행할 때 범용성을 위해 M 시리즈 활용

## 📝 관련 문제

**Question:** Which of the following instance types is best suited for workloads that require a balance of compute, memory, and network resources in Amazon EC2?

**Options:**
- A) T3
- B) M6g
- C) M5
- D) R5
- E) C5

**정답: B) M6g**

💡 추가 설명:
- **T3** - 버스터블 성능 인스턴스로 간헐적 워크로드에 적합하지만 지속적인 균형 워크로드에는 부적합
- **M5** - 균형 워크로드에 적합하지만 M6g보다 구세대로 성능과 비용 효율성이 낮음
- **R5** - 메모리 최적화 인스턴스로 메모리 집약적 워크로드 전용
- **C5** - 컴퓨팅 최적화 인스턴스로 CPU 집약적 워크로드 전용