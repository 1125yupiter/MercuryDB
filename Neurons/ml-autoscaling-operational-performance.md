---

title: ml-autoscaling-operational-performance
created: 2025-08-19
modified: 2025-08-19
tags:
- service/autoscaling
- deployment/performance
- constraint/scalability
- method/horizontal-scaling
- concept/operational-efficiency
aliases: ["ml-ops-performance", "autoscaling", "ml-scaling"]

---

# ML 모델 운영 성능 최적화를 위한 Auto Scaling 전략

## 🎯 핵심 포인트

배포된 머신러닝 모델의 운영 성능을 보장해야 하는 경우 AWS에서, Auto Scaling Group을 통한 수평적 확장 전략을 구현할 수 있다.

## 📝 설명

### Auto Scaling이 ML 모델 운영 성능에 적합한 이유

Auto Scaling Group을 통한 수평적 확장은 ML 모델의 **실제 처리 용량**을 늘려주는 직접적인 방법입니다. 트래픽 증가 시 EC2 인스턴스를 자동으로 추가하여 부하를 분산시키고, 각 요청의 응답 시간을 일정하게 유지할 수 있습니다.

핵심 특징:
- **자동화된 확장**: CloudWatch 메트릭 기반으로 자동 스케일링
- **부하 분산**: Application Load Balancer와 연동하여 효율적인 트래픽 분산
- **비용 효율성**: 필요한 만큼만 리소스를 사용하여 비용 최적화
- **고가용성**: 여러 가용영역에 인스턴스 분산으로 내결함성 확보

### 아키텍처 플로우

```
Client Request → ALB → Auto Scaling Group → EC2 Instance (ML Model)
                        ↓
                CloudWatch Metrics → Scaling Policy
                        ↓
                Add/Remove EC2 Instances
```

### Trade-offs 고려사항

**Auto Scaling 장점**:
- 실제 처리 용량 증가로 직접적인 성능 향상
- 트래픽 변화에 따른 자동 대응
- 비용 효율적인 리소스 관리
- 시스템 안정성과 가용성 향상

**Auto Scaling 단점**:
- 인스턴스 시작 시간으로 인한 지연 (워밍업 시간)
- 복잡한 스케일링 정책 설정 필요
- 상태 관리의 복잡성 증가

**Amazon Kinesis 장점**:
- 실시간 데이터 스트리밍 처리
- 높은 처리량의 데이터 수집 가능
- 실시간 분석 파이프라인 구축

**Amazon Kinesis 단점**:
- 데이터 **입력**만 제공, 모델 **처리 능력**은 증가시키지 않음
- 실시간 처리로 인한 추가 오버헤드
- 운영 성능 최적화와 직접적 연관성 부족

## 🔍 주요개념

### 기능 vs 성능 구분

- **기능적 요구사항**: "무엇을 할 수 있는가" (실시간 데이터 처리, 배치 처리 등)
- **성능 요구사항**: "얼마나 효율적으로 할 수 있는가" (처리량, 응답시간, 가용성)

### 운영 성능 핵심 지표

- **처리량(Throughput)**: 단위 시간당 처리 가능한 요청 수
- **응답시간(Latency)**: 요청부터 응답까지의 시간
- **가용성(Availability)**: 시스템이 정상 동작하는 시간 비율
- **확장성(Scalability)**: 부하 증가 시 성능 유지 능력

### 실전 적용

- E-commerce 추천 시스템의 트래픽 급증 대응
- 실시간 사기 탐지 시스템의 안정적인 서비스 제공  
- 이미지 분류 API의 대량 요청 처리

## 📝 관련 문제

**Question:** A company has deployed a machine learning model on AWS and wants to ensure efficient operational performance during varying traffic loads. Which strategy would be most effective for maintaining consistent response times and system availability?

**Options:**

- A) Using Amazon RDS as the primary data store for training data
- B) Implementing a horizontal scaling strategy by increasing the number of EC2 instances in an Auto Scaling group
- C) Using Amazon S3 to store large amounts of data for batch processing
- D) Using Amazon Kinesis to stream real-time data for inference
- E) Implementing vertical scaling by upgrading EC2 instance types

**정답: B) Implementing a horizontal scaling strategy by increasing the number of EC2 instances in an Auto Scaling group**

💡 추가 설명:

- **Option A (RDS)** - 대용량 ML 데이터 저장에 부적합하며 확장성 제한으로 운영 성능과 직접적 관련 없음
- **Option C (S3)** - 배치 처리용 저장소 역할로 실시간 운영 성능 최적화와는 거리가 있음  
- **Option D (Kinesis)** - 실시간 데이터 스트리밍 기능은 제공하지만 모델의 실제 처리 능력은 증가시키지 않음
- **Option E (Vertical Scaling)** - 단일 인스턴스 성능 향상이지만 단일 장애점 위험과 확장 한계 존재