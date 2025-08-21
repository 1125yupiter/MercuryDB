---

title: kubernetes-eks-pod-scheduling
created: 2025-08-21
modified: 2025-08-21
tags:
- service/kubernetes
- service/eks
- component/scheduler
- concept/pod
- infrastructure/container
aliases: ["k8s-scheduling", "eks-components", "pod-management"]

---

# Kubernetes EKS 파드 스케줄링과 구성 요소

## 🎯 핵심 포인트

AWS EKS에서 새로운 파드를 워커 노드에 배치해야 하는 경우, kube-scheduler가 먼저 적절한 노드를 선택하고 kubelet이 나중에 실제 파드를 생성 및 관리할 수 있다.

## 📝 설명

### Kubernetes가 파드 스케줄링에 적합한 이유

Kubernetes는 컨테이너 오케스트레이션을 위한 분산 시스템으로, 각 구성 요소가 명확한 역할 분담을 통해 효율적인 파드 관리를 수행합니다. kube-scheduler는 리소스 요구사항, 제약 조건, 친화성 규칙을 고려하여 최적의 노드를 선택하고, kubelet은 각 워커 노드에서 실제 컨테이너 생성과 상태 관리를 담당합니다.

### 아키텍처 플로우 (작업 순서)

```
1️⃣ 사용자 요청: "nginx 파드 생성해줘"
    ↓
2️⃣ API 서버: 요청 접수 및 처리
    ↓
3️⃣ kube-scheduler: "어느 노드에 배치할까? 워커노드2가 적합!"
    ↓ 배치 결정 완료
4️⃣ 워커노드2의 kubelet: "배치 지시 받음. 실제 파드 생성 시작!"
    ↓
5️⃣ 컨테이너 런타임: nginx 컨테이너 실행
    ↓
6️⃣ kubelet: "파드 생성 완료. 지속적 상태 모니터링 시작"
```

**핵심**: scheduler가 **먼저** 배치를 결정하고, kubelet이 **나중에** 실제 생성

### Trade-offs 고려사항

**kube-scheduler 장점**:
- 전체 클러스터 상태를 고려한 최적 배치
- 리소스 균형 분산 및 효율성
- 다양한 스케줄링 정책 지원

**kube-scheduler 단점**:
- 중앙 집중식 결정으로 인한 병목 가능성
- 복잡한 제약 조건 처리 시 성능 저하

**kubelet 장점**:
- 실시간 노드 상태 모니터링
- 컨테이너 생명주기 직접 관리
- 노드별 독립적 운영

**kubelet 단점**:
- 노드 장애 시 해당 노드의 모든 파드 영향
- 개별 노드 관점의 제한된 시야

## 🔍 주요개념

### 핵심 구성 요소 비교

- **kube-scheduler**: 컨트롤 플레인의 스케줄링 엔진으로, 파드를 어느 노드에 배치할지 결정하는 역할
- **kubelet**: 각 워커 노드의 에이전트로, 실제 파드를 생성하고 컨테이너 상태를 관리하는 현장 관리자
- **kube-proxy**: 네트워크 프록시로서 서비스 디스커버리와 로드 밸런싱을 담당
- **kube-controller-manager**: 다양한 컨트롤러를 실행하여 클러스터의 원하는 상태를 유지
- **etcd**: 분산 키-값 저장소로 모든 클러스터 구성 데이터를 저장

### 실전 적용

- 높은 트래픽을 처리하는 웹 애플리케이션을 여러 노드에 균등 분산 배치
- GPU가 필요한 ML 워크로드를 GPU 장착 노드에만 스케줄링
- 데이터베이스와 캐시를 동일한 가용 영역의 노드에 배치하여 지연시간 최소화

## 📝 관련 문제

**Question:** Which AWS Elastic Kubernetes Service (EKS) component is responsible for managing and scheduling pods to worker nodes in the cluster?

**Options:**

- A) kubelet
- B) kube-scheduler
- C) kube-proxy
- D) kube-controller-manager
- E) etcd

**정답: B) kube-scheduler**

💡 추가 설명:

- **kubelet** - 각 워커 노드에서 파드 실행과 상태 관리를 담당하지만 스케줄링 결정은 하지 않음
- **kube-proxy** - 네트워크 프록시와 로드 밸런싱을 담당하며 파드 스케줄링과는 무관
- **kube-controller-manager** - 클러스터 상태 관리를 담당하지만 개별 파드 스케줄링은 수행하지 않음
- **etcd** - 클러스터 데이터 저장소 역할이며 스케줄링 로직은 포함하지 않음