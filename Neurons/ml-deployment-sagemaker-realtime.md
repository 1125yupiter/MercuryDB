---

title: ml-deployment-sagemaker-realtime
created: 2025-08-21
modified: 2025-08-21
tags:
- service/sagemaker
- constraint/real-time
- deployment/hosting
- performance/low-latency
- architecture/high-availability
aliases: ["SageMaker Hosting", "ML Real-time Deployment", "AWS ML Inference"]

---

# Amazon SageMaker를 이용한 ML 모델 실시간 배포

## 🎯 핵심 포인트

SageMaker 파이프라인에서 훈련된 모델을 실시간 추론에 배포하는 경우, SageMaker Hosting Services with Auto Scaling에서 저지연과 고가용성을 동시에 확보할 수 있다.

## 📝 설명

### SageMaker Hosting Services가 실시간 ML 추론에 적합한 이유

SageMaker Hosting Services는 머신러닝 모델의 실시간 추론을 위해 특별히 설계된 완전 관리형 서비스입니다. 기술적으로는 Model 객체, Endpoint Configuration, 그리고 실제 추론을 수행하는 SageMaker Endpoint로 구성됩니다. 이 서비스는 모델 아티팩트를 자동으로 관리하고, 다양한 인스턴스 타입을 지원하며, 트래픽 패턴에 따른 자동 스케일링을 제공합니다. 특히 SageMaker 파이프라인과의 완벽한 통합을 통해 모델 훈련부터 배포까지의 전체 MLOps 워크플로우를 일관되게 관리할 수 있습니다.

### 아키텍처 플로우

```
SageMaker Hosting Services 배포 과정:
Model → Endpoint Configuration → Endpoint (실제 인스턴스)

사용자 요청 → Application Load Balancer → 
├─ SageMaker Endpoint Instance (AZ-1a) ✅
├─ SageMaker Endpoint Instance (AZ-1b) ✅  
└─ SageMaker Endpoint Instance (AZ-1c) ✅
          ↓
    Auto Scaling Group
    (트래픽 기반 자동 조정)
          ↓
    CloudWatch 모니터링
```

### 기술적 구현 세부사항

**실제 배포 과정:**
```python
# 1. Model 생성 (S3 모델 아티팩트 참조)
model = sagemaker.Model(image_uri, model_data, role)

# 2. Endpoint Configuration 생성
endpoint_config = model.create_endpoint_config(
    instance_type='ml.m5.xlarge', 
    initial_instance_count=2
)

# 3. 실제 Endpoint 배포 (이것이 추론을 수행하는 인스턴스)
predictor = model.deploy(endpoint_name='my-model-endpoint')
```

**추론 호출:**
```python
# 직접 Endpoint 이름으로 호출
response = sagemaker_runtime.invoke_endpoint(
    EndpointName='my-model-endpoint',
    Body=json.dumps(data)
)
```

### Trade-offs 고려사항

**SageMaker Hosting Services 장점**:
- ML 추론에 최적화된 인프라와 성능
- Multi-AZ 배포를 통한 고가용성 보장
- 자동 스케일링으로 트래픽 변화에 대응
- SageMaker 생태계와의 완벽한 통합
- A/B 테스팅 및 카나리 배포 지원
- GPU 인스턴스 등 다양한 컴퓨팅 옵션

**SageMaker Hosting Services 단점**:
- 지속적인 인스턴스 운영으로 인한 고정 비용
- 서버리스 대비 관리 복잡성 존재

**Lambda + Provisioned Concurrency 장점**:
- Cold start 제거로 일관된 응답 시간
- 서버리스 아키텍처의 자동 확장성
- 이벤트 기반 아키텍처와의 자연스러운 통합

**Lambda + Provisioned Concurrency 단점**:
- 메모리 제한 (최대 10GB)으로 대용량 모델 처리 어려움
- 실행 시간 제한 (15분)으로 복잡한 추론 불가
- GPU 지원 부재로 딥러닝 모델 성능 제약
- SageMaker 파이프라인과의 불완전한 통합

## 🔍 주요개념

### 핵심 배포 옵션 비교

- **SageMaker Hosting Services**: Model → EndpointConfig → Endpoint로 구성되는 전용 인스턴스 기반 실시간 추론 서비스
- **SageMaker Endpoint**: 실제 추론을 수행하는 배포된 인스턴스 (Hosting Services의 핵심 구성요소)
- **Lambda**: 서버리스 컴퓨팅 서비스로 이벤트 기반 실행에 최적화
- **EKS**: 컨테이너 오케스트레이션 플랫폼으로 복잡한 마이크로서비스 아키텍처에 적합
- **API Gateway**: API 관리 서비스로 실제 추론 인프라는 별도 필요

### 용어 정리

- **"SageMaker 호스팅으로 배포"** = 비즈니스/서비스 레벨 표현 (전체 서비스 사용)
- **"SageMaker 엔드포인트 생성"** = 기술적/구현 레벨 표현 (실제 AWS 리소스)

### 실전 적용

- **고빈도 거래 시스템**: 금융 사기 탐지 모델의 실시간 추론 (밀리초 단위 응답 필요)
- **개인화 추천 엔진**: 전자상거래 사이트의 실시간 상품 추천 (지속적 트래픽)
- **의료 진단 지원**: 의료 이미지 분석을 위한 딥러닝 모델 서빙 (GPU 가속 필요)

## 📝 관련 문제

**Question:** You are building a machine learning pipeline using Amazon SageMaker and need to deploy a trained model for real-time inference with high availability and low latency. Which of the following deployment options would be the best fit for your use case?

**Options:**

- A) AWS Lambda with Provisioned Concurrency
- B) Amazon API Gateway
- C) Amazon Elastic Kubernetes Service (EKS)
- D) Amazon SageMaker Hosting Services with Auto Scaling
- E) Amazon EC2 with Application Load Balancer

**정답: D) Amazon SageMaker Hosting Services with Auto Scaling**

💡 추가 설명:

- **AWS Lambda** - 메모리 및 실행 시간 제한으로 복잡한 ML 모델 처리에 부적합, SageMaker 파이프라인과의 통합 복잡성
- **Amazon API Gateway** - API 관리 서비스일 뿐 실제 추론을 위한 컴퓨팅 인프라 미제공
- **Amazon EKS** - 컨테이너 오케스트레이션 복잡성, ML 추론에 특화되지 않은 범용 플랫폼
- **Amazon EC2** - 인프라 관리 부담, SageMaker 생태계 이점 활용 불가