---

title: aws-cloudtrail-cloudwatch-monitoring
created: 2025-08-21
modified: 2025-08-21
tags:
- service/cloudtrail
- service/cloudwatch
- monitoring/audit
- monitoring/metrics
- aws/governance
aliases: ["cloudtrail", "cloudwatch", "aws-monitoring"]

---

# AWS CloudTrail과 CloudWatch - 감사 및 모니터링 서비스 비교

## 🎯 핵심 포인트

AWS에서 감사와 모니터링이 필요한 경우 CloudTrail은 서비스 단위 API 호출 추적에서, CloudWatch는 메트릭 단위 성능 모니터링에서 사용할 수 있다.

## 📝 설명

### CloudTrail이 감사(Audit)에 적합한 이유

CloudTrail은 AWS 계정 내 모든 API 호출을 기록하는 서비스로, "누가 무엇을 언제 했는가"에 대한 완전한 감사 추적을 제공합니다. 모든 관리 이벤트와 데이터 이벤트를 로깅하며, JSON 형태로 S3에 저장하여 장기 보관과 분석이 가능합니다.

### CloudWatch가 메트릭 모니터링에 적합한 이유

CloudWatch는 AWS 리소스의 성능 지표를 실시간으로 수집하고 모니터링하는 서비스입니다. CPU 사용률, 메모리, 네트워크 I/O 등 수치화된 데이터를 통해 시스템 상태를 파악하고, 임계값 기반 알람을 설정할 수 있습니다.

### 아키텍처 플로우

```
[사용자 API 호출] → [AWS 서비스] → [CloudTrail 로깅] → [S3 저장]
                              ↓
                        [CloudWatch 메트릭] → [알람] → [SNS 알림]
```

### Trade-offs 고려사항

**CloudTrail 장점**:
- 완전한 감사 추적 제공
- Organization 전체 계정 모니터링 가능
- 규정 준수 및 보안 분석 지원
- API 호출의 세부 컨텍스트 정보 포함

**CloudTrail 단점**:
- 실시간 성능 모니터링 불가
- 메트릭 기반 알람 설정 제한적
- 대용량 로그 데이터 저장 비용

**CloudWatch 장점**:
- 실시간 성능 메트릭 모니터링
- 임계값 기반 자동 알람
- 대시보드를 통한 시각화
- Auto Scaling과 연동 가능

**CloudWatch 단점**:
- API 호출 세부 정보 추적 불가
- 사용자별 활동 추적 제한적
- 감사 목적의 장기 보관 어려움

## 🔍 주요개념

### 서비스 단위 vs 메트릭 단위 비교

- **CloudTrail (서비스 단위)**: API 호출, 서비스 액션, 사용자 활동 등 "무엇을 했는가"에 집중
- **CloudWatch (메트릭 단위)**: 성능 지표, 사용량, 응답 시간 등 "얼마나 사용했는가"에 집중

### 실전 적용

- **보안 감사**: CloudTrail로 비정상 API 호출 패턴 분석
- **성능 모니터링**: CloudWatch로 EC2 CPU 사용률 실시간 추적
- **규정 준수**: CloudTrail 로그를 통한 SOX, PCI DSS 컴플라이언스 지원

## 📝 관련 문제

**Question:** Your organization needs to monitor and audit activities across multiple AWS accounts. You want to track who performed specific API calls and also monitor the performance metrics of your resources. Which combination of services would best meet these requirements?

**Options:**

- A) Use only AWS CloudTrail with organization trail enabled
- B) Use only Amazon CloudWatch with cross-account access
- C) Use AWS CloudTrail for API call auditing and Amazon CloudWatch for performance metrics
- D) Use AWS Config for both auditing and performance monitoring
- E) Use AWS Systems Manager for comprehensive monitoring

**정답: C) Use AWS CloudTrail for API call auditing and Amazon CloudWatch for performance metrics**

💡 추가 설명:

- **A) CloudTrail만 사용** - API 호출 추적은 가능하지만 성능 메트릭 모니터링 불가
- **B) CloudWatch만 사용** - 성능 모니터링은 가능하지만 API 호출 세부 감사 추적 불가
- **D) AWS Config 사용** - 리소스 구성 변경 추적에 특화되어 있어 성능 모니터링에 제한적
- **E) Systems Manager 사용** - 인스턴스 관리에 특화되어 있어 조직 전체 감사에는 부적합