---
title: training-sagemaker-gpu-docker.md
created: 2025-08-17
modified: 2025-08-17
tags:
- service/sagemaker
- method/deep-learning
- constraint/gpu-training
- algorithm/resnet
- deployment/custom-container
aliases: ["gpu-training", "nvidia-docker", "resnet-training", "custom-container", "sagemaker-gpu"]
---

# GPU 기반 SageMaker 훈련 – 커스텀 Docker 컨테이너와 ResNet 모델

## 🎯 핵심 포인트
커스텀 딥러닝 모델을 Amazon SageMaker에서 GPU로 훈련해야 하는 경우, **컨테이너를 nvidia-docker 호환으로 구성하면 SageMaker GPU 인스턴스에서 가속 학습을 수행할 수 있다**.

## 📝 설명

### SageMaker가 GPU 훈련에 적합한 이유
Amazon SageMaker는 **GPU 인스턴스 지원**과 **BYOC (Bring Your Own Container)** 기능을 제공하여, 커스텀 딥러닝 모델(예: ResNet)을 효율적으로 학습할 수 있다.  
GPU 훈련 시 중요한 점은 **컨테이너가 NVIDIA Container Toolkit(nvidia-docker) 호환**이어야 하며, **CUDA Toolkit은 포함**하되 **NVIDIA Driver는 포함하지 않아야** 한다. SageMaker GPU 인스턴스가 드라이버를 제공하기 때문이다.

### 아키텍처 플로우
개발자 → Dockerfile 작성 (CUDA + 딥러닝 프레임워크 포함)
→ 컨테이너 이미지 빌드 & Amazon ECR 푸시
→ SageMaker 학습 작업 생성 (GPU 인스턴스 선택)
→ SageMaker가 자동으로 NVIDIA GPU 자원 마운트
→ 모델 학습 (예: ResNet)

### Trade-offs 고려사항

**SageMaker GPU 훈련 장점**:
- GPU 가속으로 대규모 데이터셋 학습 속도 개선
- BYOC를 통한 프레임워크/알고리즘 유연성
- NVIDIA 드라이버 자동 관리

**SageMaker GPU 훈련 단점**:
- GPU 인스턴스 비용이 높음
- 컨테이너 nvidia-docker 호환성 확보 필요

**대안: EC2 NVIDIA DLAMI 장점**:
- 직접 환경 제어 가능
- SageMaker 없이도 빠른 실험 가능

**대안: EC2 NVIDIA DLAMI 단점**:
- 관리/확장성 부족
- 학습 파이프라인 통합 불리

## 🔍 주요개념

### 핵심 개념 비교
- **nvidia-docker compatible container**: GPU 가속을 지원하도록 CUDA Toolkit 포함, NVIDIA Container Runtime과 호환되는 Docker 컨테이너
- **NVIDIA drivers**: GPU 하드웨어 동작을 위한 드라이버, 컨테이너가 아닌 호스트(SageMaker GPU 인스턴스)에 설치됨

### 실전 적용
- **컴퓨터 비전**: ResNet 기반 이미지 분류 모델을 GPU로 빠르게 학습  
- **자연어 처리**: Transformer 기반 언어 모델 훈련 시 GPU 활용  
- **딥러닝 연구**: 커스텀 네트워크 실험 및 하이퍼파라미터 튜닝  

## 📝 관련 문제

**Question:**  
A company is using CPU-based instances to train deep learning models in Amazon SageMaker. For an upcoming project, the company mandates its ML team to utilize a GPU-accelerated Docker container in training a custom ResNet model. How can the team configure the container to use NVIDIA GPUs for model training?

**Options:**
- A) Launch an Amazon EC2 instance that runs on an NVIDIA Deep Learning AMI.  
- B) Ensure that your containers are nvidia-docker compatible.  
- C) Bundle the Docker image with the correct NVIDIA drivers.  
- D) Specify the `--gpu` parameter when calling the `aws sagemaker create-training-job` command.  

**정답: B) Ensure that your containers are nvidia-docker compatible**

💡 추가 설명:
- **(A)** 단순히 DLAMI를 사용하는 것은 SageMaker 컨테이너 GPU 활용과 직접 관련 없음  
- **(C)** NVIDIA 드라이버는 호스트가 제공, 컨테이너에 포함하면 안 됨  
- **(D)** SageMaker는 GPU 인스턴스를 선택하면 자동으로 GPU 리소스를 컨테이너에 연결  

---
