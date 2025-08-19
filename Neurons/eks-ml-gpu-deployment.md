---

title: eks-ml-gpu-deployment
created: 2025-08-19
modified: 2025-08-19
tags:
- service/eks
- constraint/gpu-required
- deployment/kubernetes
- usecase/machine-learning
- platform/aws
aliases: ["eks-ml", "kubernetes-ml", "gpu-ml-deployment"]

---

# Amazon EKS를 활용한 머신러닝 모델 배포

## 🎯 핵심 포인트

GPU 가속이 필요한 머신러닝 모델을 배포하는 경우 Amazon EKS에서 P3, P2, G4 등 다양한 GPU 인스턴스를 활용하여 고성능 컴퓨팅 환경을 구축할 수 있다.

## 📝 설명

### Amazon EKS가 머신러닝 워크로드에 적합한 이유

Amazon EKS(Elastic Kubernetes Service)는 완전 관리형 Kubernetes 서비스로서 머신러닝 모델의 훈련과 배포에 최적화된 환경을 제공합니다. 특히 GPU 가속이 필요한 딥러닝 워크로드에서 뛰어난 성능을 발휘하며, Kubernetes의 오케스트레이션 기능을 통해 복잡한 ML 파이프라인을 효율적으로 관리할 수 있습니다.

### 아키텍처 플로우

```
Data Sources → S3 Storage → EKS Cluster (GPU Nodes)
                                ↓
                         ML Model Training
                                ↓
                    Model Registry (ECR/S3)
                                ↓
                    Inference Deployment
                                ↓
                      Load Balancer → API Gateway
                                ↓
                        Client Applications
```

### Trade-offs 고려사항

**Amazon EKS 장점**:
- P3, P2, G4 등 다양한 GPU 인스턴스 타입 지원
- 최대 384GB 메모리까지 확장 가능한 고사양 인스턴스 제공
- Kubernetes 네이티브 오케스트레이션으로 복잡한 ML 파이프라인 관리
- Auto Scaling을 통한 동적 리소스 확장/축소
- 멀티 AZ 배포로 고가용성 보장

**Amazon EKS 단점**:
- Kubernetes 학습 곡선 및 운영 복잡성
- 소규모 단순 모델에는 과도한 오버헤드
- GPU 인스턴스의 높은 비용
- 클러스터 관리 및 보안 설정의 복잡성

**대안: Amazon SageMaker 장점**:
- 완전 관리형 서비스로 운영 부담 최소화
- 원클릭 배포 및 자동 스케일링
- 내장된 알고리즘 및 프레임워크 지원

**대안: Amazon SageMaker 단점**:
- Kubernetes 네이티브 워크로드 지원 제한
- 커스텀 오케스트레이션 로직 구현 어려움
- 기존 Kubernetes 기반 인프라와의 통합 제약

## 🔍 주요개념

### GPU 인스턴스 타입 비교

- **P3 인스턴스**: NVIDIA V100 GPU, 딥러닝 훈련에 최적화 (최대 8개 GPU)
- **P2 인스턴스**: NVIDIA K80 GPU, 범용 GPU 컴퓨팅 (최대 16개 GPU)
- **G4 인스턴스**: NVIDIA T4 GPU, 추론 워크로드에 최적화 (비용 효율적)

### 실전 적용

- **실시간 이미지 분류 서비스**: G4 인스턴스로 CNN 모델 배포, 밀리초 단위 응답시간 제공
- **대규모 언어모델 훈련**: P3 인스턴스 클러스터로 분산 훈련, 수백GB 모델 처리
- **배치 데이터 처리**: Auto Scaling으로 야간 대량 데이터 처리 후 자동 리소스 해제

## 📝 관련 문제

**Question:** A company wants to deploy machine learning models that require GPU acceleration for real-time inference. They need a solution that can automatically scale based on demand and support complex orchestration workflows. Which AWS service combination would be most suitable?

**Options:**

- A) Amazon EKS with CPU-only instances and Amazon SageMaker Endpoints
- B) Amazon EKS with GPU instances (P3, G4) and Kubernetes Auto Scaling
- C) Amazon Lambda with GPU support and Amazon API Gateway
- D) Amazon EC2 with GPU instances and manual load balancing
- E) Amazon SageMaker with CPU instances and custom containers

**정답: B) Amazon EKS with GPU instances (P3, G4) and Kubernetes Auto Scaling**

💡 추가 설명:

- **옵션 A** - CPU-only 인스턴스는 GPU 가속이 필요한 ML 모델에 부적합하며 성능 저하 발생
- **옵션 C** - Lambda는 GPU 지원하지 않으며 15분 실행 시간 제한으로 복잡한 ML 워크로드 처리 불가
- **옵션 D** - 수동 로드 밸런싱은 자동 스케일링 요구사항을 만족하지 못하고 운영 복잡성 증가
- **옵션 E** - CPU 인스턴스는 GPU 가속 요구사항을 충족하지 못해 실시간 추론 성능 저하