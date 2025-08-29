---

title: cloudwatch-alarm-autoscaling-trigger
created: 2025-08-20
modified: 2025-08-20
tags:
- service/cloudwatch
- service/ec2-autoscaling
- technique/alarm-trigger
- deployment/automatic
- constraint/metric-based
aliases: ["cloudwatch-trigger", "alarm-autoscaling", "scaling-trigger"]

---

# CloudWatch 알람을 통한 EC2 Auto Scaling 트리거 구조

## 🎯 핵심 포인트

메트릭이 임계값에 도달한 경우 CloudWatch 알람에서, Auto Scaling 정책을 트리거하여 EC2 인스턴스 수량을 자동 조절할 수 있다.

## 📝 설명

### CloudWatch 알람이 Auto Scaling 트리거에 적합한 이유

CloudWatch 알람은 메트릭 모니터링과 Auto Scaling 정책 실행을 연결하는 핵심 트리거 역할을 합니다. 알람 상태가 NORMAL에서 ALARM으로 변경될 때 미리 설정된 Auto Scaling 정책이 자동 실행되며, 이를 통해 실시간 부하 변화에 즉각 대응할 수 있습니다.

### 서비스 구성 요소

실제로는 여러 서비스가 협력하여 작동합니다:

1. **Amazon CloudWatch**: 메트릭 수집 및 알람 설정
2. **Amazon EC2 Auto Scaling**: 실제 인스턴스 수량 조절
3. **AWS Auto Scaling**: 여러 서비스 통합 관리

### 아키텍처 플로우

```
CloudWatch 메트릭 → CloudWatch 알람 → Auto Scaling 정책 → EC2 인스턴스 조절
       ↓                  ↓                ↓                ↓
   CPU 80% 초과      알람 상태 ALARM     Scale-Out 정책     인스턴스 +2개 추가
   CPU 30% 미만      알람 상태 OK       Scale-In 정책      인스턴스 -1개 제거
```

### 설정 과정

```
1. CloudWatch 알람 생성
   메트릭: EC2 CPU 사용률 > 80% (5분간)
   
2. 알람 작업(Action) 설정
   작업: Auto Scaling 정책 트리거
   
3. Auto Scaling 정책 연결
   정책: "Scale-Out-Policy" (+2 인스턴스)
   쿨다운: 300초
```

### Trade-offs 고려사항

**CloudWatch 알람 트리거 장점**:
- 실시간 메트릭 기반 자동 트리거
- 다양한 임계값 조건 설정 가능
- 여러 Auto Scaling 정책 동시 연결
- 쿨다운 기간으로 불필요한 반복 방지

**CloudWatch 알람 트리거 단점**:
- 메트릭 수집과 알람 처리 지연 (1-2분)
- 잘못된 임계값 설정 시 불안정한 확장/축소
- 복잡한 조건 설정 시 관리 복잡도 증가

**스케줄 기반 확장 장점**:
- 예측 가능한 트래픽 패턴에 효과적
- 메트릭 지연 없이 미리 대응

**스케줄 기반 확장 단점**:
- 예상치 못한 트래픽 변화에 대응 불가
- 고정된 시간 기반으로 유연성 부족

## 🔍 주요개념

### 핵심 구성 요소

- **CloudWatch 알람**: 메트릭 임계값 도달 시 Auto Scaling 정책을 트리거하는 방아쇠 역할
- **Auto Scaling 정책**: 실제 인스턴스 증감 작업을 정의한 규칙 (Scale-Out/Scale-In)
- **알람 작업(Action)**: CloudWatch 알람에서 설정하는 트리거 명령
- **쿨다운 기간**: 스케일링 후 다음 작업까지의 대기 시간

### 실전 적용

- **웹 애플리케이션**: CPU 사용률 80% 초과 시 웹서버 인스턴스 2개 추가
- **API 서버**: 응답 시간 500ms 초과 시 백엔드 서버 확장
- **배치 처리**: 큐 메시지 수 100개 초과 시 워커 인스턴스 증가

## 📝 관련 문제

**Question:** You need to automatically scale EC2 instances based on CPU utilization. How should you configure CloudWatch alarms to trigger Auto Scaling actions?

**Options:**

- A) Set up CloudWatch alarms to send notifications to administrators for manual scaling
- B) Configure CloudWatch alarms to directly modify EC2 instance types
- C) Create CloudWatch alarms that trigger Auto Scaling policies as alarm actions
- D) Use CloudWatch alarms to automatically purchase Reserved Instances
- E) Set CloudWatch alarms to restart EC2 instances when CPU is high

**정답: C) Create CloudWatch alarms that trigger Auto Scaling policies as alarm actions**

💡 추가 설명:

- **A) 관리자 알림** - 수동 개입 필요로 자동화 목적에 부합하지 않음
- **B) 인스턴스 타입 변경** - CloudWatch 알람으로 직접 불가능하며 Scale-Up 방식
- **D) Reserved Instance 구매** - 장기 계약 방식으로 동적 확장과 무관
- **E) 인스턴스 재시작** - 수량 조절이 아닌 재부팅으로 확장 효과 없음