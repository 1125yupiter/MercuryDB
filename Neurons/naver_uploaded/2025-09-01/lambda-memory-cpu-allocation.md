---

title: lambda-memory-cpu-allocation
created: 2025-08-21
modified: 2025-08-21
tags:
- service/lambda
- resource/memory
- resource/cpu
- allocation/proportional
- config/manual-memory
aliases: ["lambda-resource", "lambda-mem-cpu", "lambda-allocation"]

---

# AWS Lambda 메모리-CPU 할당 방식과 리소스 구성

## 🎯 핵심 포인트

AWS Lambda에서 메모리를 수동으로 설정한 경우, CPU와 네트워크 대역폭은 메모리 설정에 비례하여 자동 할당된다.

## 📝 설명

### AWS Lambda가 서버리스 컴퓨팅에 적합한 이유

AWS Lambda는 서버 관리 없이 코드를 실행할 수 있는 서버리스 컴퓨팅 서비스입니다. 사용자는 메모리만 직접 설정하고, CPU와 네트워크 대역폭은 자동으로 비례 할당됩니다. 이는 복잡한 인프라 구성 없이도 효율적인 리소스 활용을 가능하게 합니다.

### 아키텍처 플로우

```
개발자 설정
    ↓
메모리 할당 (128MB - 10,240MB)
    ↓
AWS 자동 할당
    ↓
CPU 할당 (메모리 비례) ← 1,769MB = 1 vCPU
    ↓                   ← 10,240MB = 6 vCPU
네트워크 대역폭 할당 (메모리 비례)
    ↓
함수 실행
```

### Trade-offs 고려사항

**메모리 기반 할당 장점**:
- 간단한 구성 (메모리만 설정)
- 자동 비례 할당으로 복잡성 제거
- 비용 예측 가능성
- 성능 튜닝 용이성

**메모리 기반 할당 단점**:
- CPU 직접 제어 불가능
- 메모리 사용량과 CPU 요구량 불일치 시 비효율
- 세밀한 리소스 최적화 제한

**EC2 직접 제어 장점**:
- CPU, 메모리 독립적 설정
- 정밀한 리소스 튜닝
- 다양한 인스턴스 타입 선택

**EC2 직접 제어 단점**:
- 서버 관리 복잡성
- 자동 스케일링 구성 필요
- 유휴 시간 비용 발생

## 🔍 주요개념

### 리소스 할당 방식 비교

- **수동 할당**: 메모리 (128MB ~ 10,240MB, 개발자 직접 설정)
- **자동 할당**: CPU (메모리에 비례, 1,769MB = 1 vCPU)
- **자동 할당**: 네트워크 대역폭 (메모리에 비례)

### 실전 적용

- 메모리 집약적 함수: 높은 메모리 설정으로 충분한 CPU도 확보
- CPU 집약적 함수: 필요한 CPU를 위해 메모리를 상향 조정
- 네트워크 집약적 함수: 대역폭 확보를 위한 메모리 최적화

## 📝 관련 문제

**Question:** Which of the following statements is true about AWS Lambda and its resource allocation?

**Options:**

- A) Lambda automatically allocates CPU power, network bandwidth, and memory based on the function's configuration
- B) The developer can allocate a fixed amount of network bandwidth and memory to their Lambda function, and CPU power is automatically adjusted based on the function's traffic
- C) The developer can allocate a fixed amount of CPU power and memory to their Lambda function, and network bandwidth is automatically adjusted based on the function's traffic
- D) AWS Lambda does not provide any control over CPU power, network bandwidth, or memory allocation
- E) Lambda automatically scales CPU power, network bandwidth, and memory allocation based on the function's usage, and the developer has no control over the resources

**정답: A) Lambda automatically allocates CPU power, network bandwidth, and memory based on the function's configuration**

💡 추가 설명:

- **옵션 B** - CPU는 트래픽이 아닌 메모리 설정에 비례하여 할당됨
- **옵션 C** - 개발자는 CPU를 직접 설정할 수 없고, 메모리만 설정 가능
- **옵션 D** - 메모리는 개발자가 직접 제어 가능 (128MB-10,240MB)
- **옵션 E** - 사용량 기반이 아닌 구성(메모리 설정) 기반 할당임