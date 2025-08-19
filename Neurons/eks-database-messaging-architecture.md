---
title: eks-database-messaging-architecture
created: 2025-08-19
modified: 2025-08-19
tags:
- service/eks
- service/rds
- service/sqs
- deployment/kubernetes
- architecture/microservices
aliases: ["EKS-DB-Queue", "Kubernetes-AWS-Services", "Container-Architecture"]

---

# Amazon EKS에서 데이터베이스와 메시지 큐 통합 아키텍처

## 🎯 핵심 포인트

Amazon EKS에서 ML 애플리케이션을 배포하는 경우 Amazon RDS와 Amazon SQS에서, 안정적인 데이터베이스와 메시지 큐 기능을 제공할 수 있다.

## 📝 설명

### Amazon RDS와 SQS가 EKS 환경에 적합한 이유

**Amazon RDS**는 완전 관리형 관계형 데이터베이스 서비스로, EKS 클러스터 내 컨테이너화된 애플리케이션과의 표준 데이터베이스 연결을 지원합니다. VPC 내에서 네트워크 연결을 통해 안전하게 접근할 수 있으며, IAM roles for service accounts (IRSA)를 통한 보안 인증을 제공합니다.

**Amazon SQS**는 AWS의 완전 관리형 메시지 큐잉 서비스로, 마이크로서비스 간 비동기 통신과 애플리케이션 구성 요소 간 디커플링을 효과적으로 처리합니다. 높은 가용성과 확장성을 제공하며, EKS 워크로드와의 통합이 매우 원활합니다.

### 아키텍처 플로우

```
[EKS Cluster]
    ↓ (Database Queries)
[Amazon RDS Instance]
    ↓ (Connection Pool)
[Application Pods] 
    ↓ (Message Publishing)
[Amazon SQS Queue]
    ↓ (Message Processing)
[Worker Pods] → [Scaling Based on Queue Depth]
```

### Trade-offs 고려사항

**Amazon RDS 장점**:
- 완전 관리형 서비스로 운영 오버헤드 최소화
- 자동 백업, 패치, 모니터링 제공
- 다양한 데이터베이스 엔진 지원 (MySQL, PostgreSQL, Oracle 등)
- 읽기 전용 복제본을 통한 읽기 성능 향상

**Amazon RDS 단점**:
- 관계형 데이터베이스 구조에 제한됨
- NoSQL 요구사항이 있는 경우 부적합
- 비용이 DynamoDB보다 높을 수 있음

**Amazon SQS 장점**:
- 메시지 전달 보장 (at-least-once delivery)
- 자동 확장 및 고가용성
- 데드 레터 큐를 통한 오류 메시지 처리
- FIFO 큐 지원으로 순서 보장 가능

**Amazon SQS 단점**:
- 실시간 스트리밍에는 Kinesis가 더 적합
- 복잡한 라우팅 기능은 SNS와 조합 필요
- 메시지 크기 제한 (256KB)

**대안 서비스 비교**:

**Amazon DynamoDB**:
- 장점: NoSQL 확장성, 서버리스 모델
- 단점: 관계형 데이터 처리에 제한적

**Amazon Aurora**:
- 장점: 클라우드 네이티브, 높은 성능
- 단점: MySQL/PostgreSQL 호환성에 제한

**Amazon Kinesis**:
- 장점: 실시간 스트림 처리
- 단점: 단순한 메시지 큐잉에는 과도한 복잡성

## 🔍 주요개념

### 핵심 개념 비교

- **Amazon RDS**: AWS의 완전 관리형 관계형 데이터베이스 서비스로, 전통적인 RDBMS 기능과 클라우드 확장성을 제공
- **Amazon SQS**: 분산 시스템과 마이크로서비스 간 안전한 메시지 전달을 위한 완전 관리형 큐 서비스
- **EKS 통합**: Kubernetes Service Account와 IAM Role 연결을 통한 보안 인증 및 AWS Load Balancer Controller를 통한 트래픽 관리

### 실전 적용

- **ML 모델 추론 파이프라인**: 요청을 SQS로 큐잉하고, 결과를 RDS에 저장하여 배치 처리 최적화
- **마이크로서비스 아키텍처**: 서비스 간 비동기 통신을 SQS로 처리하고, 공유 데이터를 RDS에서 관리
- **이벤트 기반 처리**: 사용자 액션을 SQS 메시지로 처리하고, 상태 정보를 RDS에 지속적으로 저장

## 📝 관련 문제

**Question:** You are working on a machine learning project and need to deploy your application to Amazon Elastic Kubernetes Service (Amazon EKS). The application will require a database and a message queue. What AWS service can you use to provide the required database and message queue functionality for your application in Amazon EKS?

**Options:**
- A) Amazon DynamoDB and Amazon Kinesis
- B) Amazon Aurora and Amazon SNS  
- C) Amazon RDS and Amazon SQS
- D) Amazon DocumentDB and Amazon S3
- E) Amazon ElastiCache and Amazon EventBridge

**정답: C) Amazon RDS and Amazon SQS**

💡 추가 설명:

- **Amazon DynamoDB and Amazon Kinesis** - DynamoDB는 NoSQL로 관계형 데이터베이스가 필요한 경우 부적합하며, Kinesis는 실시간 스트리밍에 특화되어 단순 메시지 큐잉에는 과도함
- **Amazon Aurora and Amazon SNS** - Aurora도 좋은 선택이지만 SNS는 pub/sub 모델로 메시지 큐잉보다는 알림 서비스에 더 적합
- **Amazon DocumentDB and Amazon S3** - DocumentDB는 MongoDB 호환 문서 데이터베이스이며, S3는 객체 스토리지로 메시지 큐 역할에 부적합
- **Amazon ElastiCache and Amazon EventBridge** - ElastiCache는 캐싱 서비스로 주 데이터베이스 역할에 제한적이며, EventBridge는 이벤트 라우팅에 특화