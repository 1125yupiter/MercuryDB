---
title: eks-security-iam-rbac
created: 2025-08-20
modified: 2025-08-20
tags:
- service/eks
- security/iam-rbac
- deployment/kubernetes
- constraint/access-control
- platform/aws
aliases: ["EKS Security", "Kubernetes RBAC", "IAM-RBAC"]
---

# Amazon EKS 보안 - IAM과 RBAC 기반 접근 제어

## 🎯 핵심 포인트

머신러닝 팀이 Kubernetes 애플리케이션을 배포해야 하는 경우 Amazon EKS에서, IAM과 Kubernetes RBAC의 이중 보안 메커니즘으로 클러스터 리소스에 대한 세밀한 접근 제어를 구현할 수 있다.

## 📝 설명

### Amazon EKS가 엔터프라이즈 Kubernetes 배포에 적합한 이유

Amazon EKS는 완전관리형 Kubernetes 서비스로서 AWS IAM과 Kubernetes RBAC을 통합하여 다층 보안 아키텍처를 제공합니다. 클러스터 생성 시 기본적으로 AWS IAM이 클러스터 수준의 접근을 제어하고, Kubernetes RBAC이 클러스터 내부 리소스에 대한 세밀한 권한을 관리합니다.

EKS는 관리형 컨트롤 플레인을 통해 고가용성과 자동 스케일링을 제공하며, VPC 네트워크 격리, 전송 중/저장 시 암호화, CloudTrail을 통한 감사 로깅 등 엔터프라이즈급 보안 기능을 기본으로 탑재합니다.

### 아키텍처 플로우

```
사용자/애플리케이션 요청
       ↓
AWS IAM 인증 (클러스터 접근 권한)
       ↓
Kubernetes API 서버
       ↓
Kubernetes RBAC 인가 (네임스페이스/리소스 권한)
       ↓
워커 노드의 Pod/서비스 실행
       ↓
보안 그룹 + 네트워크 정책으로 트래픽 제어
```

### Trade-offs 고려사항

**Amazon EKS 장점**:
- IAM과 RBAC 이중 보안으로 세밀한 접근 제어 가능
- 관리형 컨트롤 플레인으로 운영 부담 최소화
- AWS 서비스들과의 네이티브 통합 (ALB, NLB, EFS, EBS 등)
- 자동 보안 패치 및 업데이트
- CloudTrail, CloudWatch와의 완전한 통합

**Amazon EKS 단점**:
- 컨트롤 플레인 비용 ($0.10/hour)
- AWS 생태계 종속성
- 온프레미스 대비 네트워크 레이턴시

**Self-managed Kubernetes 장점**:
- 완전한 제어권과 커스터마이징 가능
- 클라우드 벤더 종속성 없음
- 컨트롤 플레인 비용 절약

**Self-managed Kubernetes 단점**:
- 복잡한 보안 설정과 지속적 관리 필요
- 고가용성 구성의 복잡성
- 보안 패치 및 업데이트 수동 관리

## 🔍 주요개념

### 보안 메커니즘 비교

- **AWS IAM**: AWS 리소스 수준에서 클러스터에 대한 접근을 제어하고, 사용자/역할 기반으로 클러스터 생성, 삭제, 수정 권한을 관리
- **Kubernetes RBAC**: 클러스터 내부에서 네임스페이스, Pod, 서비스 등 Kubernetes 리소스에 대한 세밀한 권한을 역할 기반으로 제어

### 실전 적용

- **개발팀별 네임스페이스 격리**: IAM 역할을 통해 클러스터 접근 후, RBAC으로 특정 네임스페이스에만 권한 부여
- **CI/CD 파이프라인 보안**: 서비스 계정과 IAM 역할 연동으로 배포 프로세스의 최소 권한 원칙 적용
- **운영팀의 클러스터 관리**: IAM 정책으로 클러스터 관리 권한 부여하고, RBAC으로 모든 네임스페이스 접근 허용

## 📝 관련 문제

**Question:** A machine learning team is working on a project that requires the deployment of Kubernetes applications. The team has decided to use Amazon Elastic Kubernetes Service (EKS) to deploy and manage the Kubernetes clusters. The team is concerned about the security of the EKS cluster and wants to use the best practices to secure the cluster. Which of the following statements is true regarding EKS security?

**Options:**

- A) EKS clusters are protected by default with AWS IAM and Kubernetes RBAC to manage access to resources
- B) EKS clusters do not support network policies, and the security must be managed by configuring security groups for worker nodes
- C) EKS clusters are secured by default and do not require any additional security configurations
- D) EKS clusters automatically create and manage SSL/TLS certificates for secure communication between the Kubernetes API server and worker nodes

**정답: A) EKS clusters are protected by default with AWS IAM and Kubernetes RBAC to manage access to resources**

💡 추가 설명:

- **Option B** - EKS는 Calico, Cilium 등 CNI 플러그인을 통해 네트워크 정책을 완벽 지원하며, 보안 그룹만으로는 세밀한 Pod 간 트래픽 제어가 불가능
- **Option C** - 기본 보안이 제공되지만 Pod Security Standards, 네트워크 정책, 로깅 설정 등 추가 보안 구성이 실무에서는 필수
- **Option D** - API 서버와 워커 노드 간 TLS는 기본 제공되지만, 애플리케이션별 인증서는 별도 구성이 필요