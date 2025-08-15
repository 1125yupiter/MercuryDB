# MercuryDB 구축 가이드

## 🎯 MercuryDB 비전
MLS-C01 자격증 준비, 포트폴리오 구축, 미래 자산 창출이라는 세 가지 가치를 동시에 달성하는 ML 솔루션 지식 네트워크

### 핵심 목표
- **단기**: MLS-C01 시험 준비를 통한 체계적 지식 정리
- **중기**: AI Product Manager/Owner 포트폴리오 구축
- **장기**: ML 솔루션 설계의 표준 프레임워크 창출

---

## 📐 태그 시스템 설계

### 태그 형식: 네임스페이스 방식 (콜론 구분)
```
#category:subcategory
예: #problem:regression, #service:sagemaker
```

### 핵심 태그 카테고리

#### 1. Pipeline Phase (파이프라인 단계)
```
#phase:collection       - 데이터 수집
#phase:preprocessing    - 데이터 전처리
#phase:training        - 모델 학습
#phase:tuning          - 하이퍼파라미터 튜닝
#phase:deployment      - 모델 배포
#phase:monitoring      - 모니터링 및 유지보수
```

#### 2. Problem Type (문제 유형)
```
#problem:regression     - 회귀 문제
#problem:classification - 분류 문제
#problem:clustering    - 클러스터링
#problem:forecasting   - 시계열 예측
#problem:anomaly       - 이상 탐지
#problem:recommendation - 추천 시스템
```

#### 3. Method (방법론)
```
#method:supervised     - 지도 학습
#method:unsupervised   - 비지도 학습
#method:reinforcement  - 강화 학습
#method:deep-learning  - 딥러닝
#method:ensemble       - 앙상블
```

#### 4. Constraints (제약 조건)
```
#constraint:real-time        - 실시간 처리
#constraint:batch           - 배치 처리
#constraint:near-real-time  - 준실시간 (1시간 이내)
#constraint:cost-sensitive  - 비용 민감
#constraint:performance-critical - 성능 중요
#constraint:high-volume     - 대용량 처리
#constraint:low-latency     - 낮은 지연시간
```

#### 5. Environment (환경)
```
#env:aws          - AWS 클라우드
#env:azure        - Azure 클라우드
#env:gcp          - GCP 클라우드
#env:on-premise   - 온프레미스
#env:hybrid       - 하이브리드

#arch:serverless    - 서버리스 아키텍처
#arch:containerized - 컨테이너 기반
#arch:distributed   - 분산 시스템
```

#### 6. Industry & Compliance (산업 및 규제)
```
#industry:healthcare    - 의료/헬스케어
#industry:finance      - 금융
#industry:retail       - 리테일
#industry:manufacturing - 제조업
#industry:media        - 미디어/엔터테인먼트

#compliance:gdpr    - GDPR 규제
#compliance:hipaa   - HIPAA 규제
#compliance:sox     - SOX 규제
#compliance:pci-dss - PCI-DSS 규제
```

#### 7. AWS Services
```
#service:sagemaker    - Amazon SageMaker
#service:comprehend   - Amazon Comprehend
#service:forecast     - Amazon Forecast
#service:personalize  - Amazon Personalize
#service:textract     - Amazon Textract
#service:rekognition  - Amazon Rekognition
#service:glue        - AWS Glue
#service:kinesis     - Amazon Kinesis
```

---

## 📝 마스터 템플릿

```markdown
---
title: {{title}}
created: {{date}}
type: solution-pattern | service-guide | compliance-doc | architecture-pattern
status: draft | reviewed | validated | production

# === 태그 선택 가이드 ===
# 아래에서 해당하는 태그만 남기고 나머지는 삭제

phase_tags: 
  #phase:collection | #phase:preprocessing | #phase:training | #phase:tuning | #phase:deployment | #phase:monitoring

problem_tags:
  #problem:regression | #problem:classification | #problem:clustering | #problem:forecasting | #problem:anomaly | #problem:recommendation

method_tags:
  #method:supervised | #method:unsupervised | #method:reinforcement | #method:deep-learning | #method:ensemble

constraint_tags:
  #constraint:real-time | #constraint:batch | #constraint:near-real-time
  #constraint:cost-sensitive | #constraint:performance-critical
  #constraint:high-volume | #constraint:low-latency

environment_tags:
  #env:aws | #env:azure | #env:gcp | #env:on-premise | #env:hybrid
  #arch:serverless | #arch:containerized | #arch:distributed

industry_tags:
  #industry:healthcare | #industry:finance | #industry:retail | #industry:manufacturing | #industry:media

compliance_tags:
  #compliance:gdpr | #compliance:hipaa | #compliance:sox | #compliance:pci-dss

service_tags:
  #service:sagemaker | #service:comprehend | #service:forecast | #service:personalize | #service:textract
---

## 📋 시나리오
<!-- MLS-C01 문제 또는 실제 비즈니스 시나리오 -->

## 🎯 핵심 포인트
<!-- 이 케이스에서 반드시 기억해야 할 내용 -->

## 🔧 솔루션
<!-- 구체적인 해결 방법 및 아키텍처 -->

## ⚖️ Trade-offs
<!-- 장단점 및 대안 -->

## 🔗 연관 패턴
<!-- [[다른 관련 노트]] 링크 -->

## 📚 참조
<!-- MLS-C01 관련 문제, AWS 문서 링크 -->

## 💡 실전 노트
<!-- 실제 적용 시 추가 고려사항 -->
```

---

## 🧠 학습 중심 수동 태깅 전략

### 태깅이 곧 학습이다
- **자동화 금지**: 태그를 직접 선택하는 과정이 ML 개념을 내재화하는 핵심
- **의사결정 연습**: 각 태그 선택이 작은 아키텍처 결정
- **오류도 자산**: 잘못된 태그 선택에서 더 깊은 이해 획득

### 태그 결정 과정 기록 예시
```markdown
문제: "실시간 신용카드 사기 탐지 시스템 구축"

태그 결정 과정:
✓ #problem:anomaly 
  → 정상 패턴에서 벗어난 거래 탐지
✓ #constraint:real-time 
  → 거래 승인 전 즉시 판단 필요
✓ #constraint:low-latency 
  → 고객 경험 저하 방지 (100ms 이내)
✗ #problem:classification 
  → 단순 이진 분류가 아닌 이상 패턴 탐지
```

---
# 디렉토리 구조 - 심플하게

/MercuryDB
├── 📁 00-Meta/
│   ├── README.md
│   ├── TagRules.md
│   ├── Templates/
│   └── Index.md          # 선택적: 주요 entry point들만 모아둔 목록
│
├── 📁 Neurons/            # 모든 지식 노트가 여기에
│   ├── sagemaker-hyperparameter-tuning.md
│   ├── real-time-fraud-detection.md
│   ├── comprehend-medical-phi.md
│   ├── kinesis-vs-kafka.md
│   ├── gdpr-compliance-checklist.md
│   └── ... (수백 개의 노트들)
│
└── 📁 Inbox/              # 임시 메모
    └── quick-notes.md

---

*"지식을 정리하는 것이 아니라, 지식의 네트워크를 구축하는 것"*