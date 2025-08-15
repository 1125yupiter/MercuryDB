# EC2 Instance Types - Balanced Workload Selection


## 🎯 핵심 포인트
> AWS EC2에서 컴퓨팅, 메모리, 네트워크 균형이 필요한 워크로드에는 M6g 인스턴스가 최적 (M5 대비 40% 향상된 가격 대비 성능)

## 📝 설명
**M 시리즈가 balanced workload에 최적인 이유:**

EC2에서 컴퓨팅, 메모리, 네트워크 리소스의 균형이 필요한 워크로드에는 M 시리즈(General Purpose) 인스턴스를 사용합니다. M 시리즈는 특정 리소스에 편중되지 않고 모든 리소스를 균등하게 제공하도록 설계되었습니다.

**AWS 인스턴스 넘버링 규칙:**
AWS는 인스턴스 세대가 높을수록 더 나은 성능과 비용 효율성을 제공합니다. 따라서 M6g > M5 순으로 성능이 우수하며, M6g는 AWS Graviton2 프로세서 기반으로 기존 x86 기반 M5 대비 최대 40% 향상된 가격 대비 성능을 보여줍니다.

**Trade-offs 고려사항:**
- **M6g 장점**: 최신 세대의 뛰어난 성능, Graviton2 프로세서의 에너지 효율성, 향상된 비용 효율성
- **M6g 단점**: ARM 아키텍처로 인한 일부 x86 전용 소프트웨어와의 호환성 이슈 가능성
- **M5 장점**: 검증된 x86 아키텍처로 광범위한 소프트웨어 호환성
- **M5 단점**: M6g 대비 상대적으로 낮은 성능과 비용 효율성

## 🔍 주요개념 및 관련개념

**EC2 인스턴스 패밀리별 특화 용도:**
- **M 시리즈**: 범용(General Purpose) - 컴퓨팅, 메모리, 네트워크 균형
- **C 시리즈**: 컴퓨팅 집약적 워크로드 (Compute Optimized)
- **R 시리즈**: 메모리 집약적 워크로드 (Memory Optimized)
- **T 시리즈**: 버스터블 성능 워크로드 (Burstable Performance)

**세대별 차이:**
- M6g > M5: 더 높은 번호 = 더 나은 성능과 비용 효율성
- AWS Graviton2 프로세서 vs 기존 x86 아키텍처

**실전 적용:**
- AWS에서 인스턴스 번호가 높을수록 일반적으로 더 나은 성능과 효율성 제공
- M 시리즈는 "범용"의 대표격으로 대부분의 일반적인 애플리케이션에 적합
- ARM 기반 Graviton2의 호환성 검토 후 도입 권장

## 📝 관련 문제

**Question:**
Which of the following instance types is best suited for workloads that require a balance of compute, memory, and network resources in Amazon EC2?

**Options:**
- A) T3
- B) M6g
- C) M5
- D) R5
- E) C5

---

## 🏷️ 태그 정리
**Environment:** `#env:aws`  
**Service:** `#service:ec2`  
**Phase:** `#phase:deployment`  
**Instance-Type:** `#instance:general-purpose`