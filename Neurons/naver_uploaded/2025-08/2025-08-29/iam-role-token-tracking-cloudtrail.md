---

title: iam-role-token-tracking-cloudtrail
created: 2025-08-20 
modified: 2025-08-20 
tags:
- service/iam
- security/temporary-credentials
- monitoring/cloudtrail
- access/assume-role
- audit/token-tracking
aliases: ["iam-token-tracking", "role-session-monitoring", "assume-role-audit"]

---

# IAM Role 임시 토큰 관리 및 CloudTrail 추적

## 🎯 핵심 포인트

사용자가 IAM Role을 assume하여 임시 토큰을 받아 작업하는 경우 CloudTrail에서 토큰 발급부터 실제 사용까지의 모든 활동을 추적하고 감사할 수 있다.

## 📝 설명

### IAM Role 임시 토큰 메커니즘이 보안 관리에 적합한 이유

IAM Role의 임시 토큰 시스템은 AWS의 핵심 보안 메커니즘으로, 다음과 같은 특징을 제공합니다:

- **Assume 기반 권한 위임**: 사용자가 필요할 때만 특정 역할을 "가정"하여 권한 획득
- **임시 자격 증명**: 15분~12시간의 제한된 유효 시간을 가진 토큰 발급
- **자동 만료**: 별도 회수 작업 없이 시간 경과 시 자동 무효화
- **완전한 추적성**: CloudTrail을 통한 토큰 생명주기 전체 모니터링

각 토큰은 고유한 Access Key ID를 가지며, 매번 새로운 assume 요청 시 완전히 다른 자격 증명이 생성됩니다.

### 아키텍처 플로우

```
사용자 A → AssumeRole 요청 → AWS STS
    ↓                           ↓
Trust Policy 검증 ← IAM Role    임시 토큰 발급
    ↓                           ↓
AssumeRole 권한 확인            AccessKeyID: ASIA123...
    ↓                           SessionToken: xyz789...
CloudTrail 로깅 ← 토큰으로 S3 작업 수행
    ↓
토큰 만료 (자동) → 권한 소멸
```

### Trade-offs 고려사항

**IAM Role + 임시 토큰 장점**:
- 자동 만료로 보안 위험 최소화
- 매번 새로운 자격 증명으로 이전 토큰과 완전 분리
- CloudTrail을 통한 상세한 감사 추적
- AWS 서비스와의 네이티브 통합

**IAM Role + 임시 토큰 단점**:
- 양방향 권한 설정 필요 (Role Trust Policy + User AssumeRole 권한)
- 토큰 만료 시 재발급 과정 필요
- 초기 설정의 복잡성

**IAM User + 액세스 키 장점**:
- 단순한 설정 (사용자에게 직접 권한 부여)
- 영구적 자격 증명으로 재인증 불필요

**IAM User + 액세스 키 단점**:
- 장기간 유효한 자격 증명으로 높은 보안 위험
- 키 로테이션의 운영 복잡성
- 하드코딩 시 코드 노출 위험

## 🔍 주요개념

### 토큰 관리 메커니즘

- **AssumeRole**: 특정 역할의 권한을 임시로 획득하는 행위
- **Session Duration**: Role에서 설정한 최대 허용 시간과 실제 요청 시간 중 작은 값 적용
- **Token Refresh**: 만료 후 새로운 assume 요청으로 완전히 새로운 토큰 발급
- **Trust Policy**: Role을 누가 assume할 수 있는지 정의하는 정책

### CloudTrail 추적 범위

- **AssumeRole 이벤트**: 토큰 발급 시점과 발급받은 사용자 정보
- **실제 작업 이벤트**: 토큰을 사용한 모든 AWS API 호출
- **Session 정보**: 각 토큰의 고유 ID와 사용 이력
- **역추적 가능**: 특정 작업에서 토큰 발급까지 완전한 연결 추적

### 실전 적용

- **보안 감사**: 의심스러운 활동 발생 시 토큰 기반 상세 추적
- **컴플라이언스 보고**: 누가 언제 어떤 권한으로 무엇을 했는지 증명
- **운영 모니터링**: 현재 활성 세션과 과거 사용 패턴 분석

## 📝 관련 문제

**Question:** An organization needs to implement a secure access pattern for developers who occasionally need elevated permissions to access production S3 buckets. The solution should provide detailed audit trails and minimize long-term security risks. What is the most appropriate approach?

**Options:**

- A) Create individual IAM users for each developer with permanent S3 access permissions
- B) Configure IAM roles with S3 permissions and require developers to assume roles when needed, with all activities logged in CloudTrail
- C) Use a shared IAM user account with S3 permissions and rotate the access keys monthly
- D) Grant temporary S3 bucket policies that allow public access during maintenance windows
- E) Create long-term access keys and store them in a shared password manager

**정답: B) Configure IAM roles with S3 permissions and require developers to assume roles when needed, with all activities logged in CloudTrail**

💡 추가 설명:

- **Option A** - 영구적 권한으로 불필요한 지속적 접근 위험과 권한 관리 복잡성 증가
- **Option C** - 공유 계정 사용으로 개별 사용자 추적 불가능하며 액세스 키 노출 위험
- **Option D** - 퍼블릭 액세스는 심각한 보안 위험으로 프로덕션 환경에 부적합
- **Option E** - 장기간 유효한 자격 증명으로 높은 보안 위험과 공유로 인한 추적성 부족