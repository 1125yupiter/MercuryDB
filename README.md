# MercuryDB 🧠

> **ML 솔루션 설계를 위한 지식 네트워크**  
> *“Not just organizing knowledge, but building a neural network of ML solutions”*

## 📌 What is MercuryDB?

MercuryDB는 AWS Machine Learning 솔루션 패턴을 체계화한 지식 네트워크입니다. 시작은 MLS-C01 자격증 준비였지만, 궁극적으로는 **기업의 비즈니스 컨텍스트를 분석하여 최적의 ML 솔루션을 자동으로 설계하는 AI 시스템**으로 진화하고 있습니다.

### 🎯 핵심 목표

- **단기**: MLS-C01 자격증 대비 및 ML 개념 내재화
- **중기**: 실제 적용 가능한 ML 솔루션 패턴 축적
- **장기**: CEO 인터뷰 → 자동 ML 솔루션 설계 시스템

## 🏗️ 구조

```
/MercuryDB
├── 📁 00-Meta/          # 시스템 규칙 및 템플릿
│   ├── README.md        # 이 문서
│   ├── TagRules.md      # 태그 시스템 정의
│   ├── FilenameFormat.md # 파일명 규칙
│   └── Templates/       # 문서 템플릿
│
├── 📁 Neurons/          # 모든 지식 노트 (폴더 구분 없음)
│   └── *.md            # 각 노트는 하나의 뉴런
│
└── 📁 Inbox/           # 임시 메모 및 미정리 노트
```

### 왜 폴더 구분이 없나요?

MercuryDB는 뇌의 뉴런처럼 작동합니다. 지식은 폴더가 아닌 **태그와 링크**로 연결됩니다. 하나의 노트가 동시에 여러 역할(패턴, 서비스 가이드, 시나리오)을 수행할 수 있습니다.

## 🏷️ 태그 시스템

### 핵심 카테고리

- `#phase:` - 파이프라인 단계 (collection, preprocessing, training…)
- `#problem:` - 문제 유형 (classification, regression, anomaly…)
- `#service:` - AWS 서비스 (sagemaker, comprehend, kinesis…)
- `#constraint:` - 제약 조건 (real-time, cost-sensitive, high-volume…)
- `#industry:` - 산업 분야 (healthcare, finance, retail…)
- `#compliance:` - 규제 (gdpr, hipaa, sox…)
- `#method:` - 방법론 (supervised, deep-learning, ensemble…)

### 태그 예시

```yaml
tags:
  - #problem:classification
  - #service:sagemaker
  - #constraint:real-time
  - #industry:finance
```

## 📝 파일명 규칙

### 기본 포맷

```
[problem]-[service]-[constraint].md
```

### 예시

- `classification-sagemaker-realtime.md`
- `anomaly-kinesis-sagemaker.md`
- `forecast-retail-demand.md`
- `compare-batch-realtime.md`

자세한 규칙은 [FilenameFormat.md](./00-Meta/FilenameFormat.md) 참조

## 🚀 시작하기

### 1. 새 노트 작성

1. 문제/시나리오 분석
2. 태그 결정
3. 파일명 생성 (태그 기반)
4. 템플릿 사용하여 문서 작성
5. 관련 노트와 링크 연결

### 2. 노트 찾기

- **파일명 검색**: 키워드로 직접 검색
- **태그 검색**: `#service:sagemaker` 등으로 필터링
- **Graph View**: 시각적 연결 관계 탐색
- **Dataview 쿼리**: 복잡한 조건으로 검색

### 3. Dataview 쿼리 예시

```dataview
TABLE tags, created
FROM "Neurons"
WHERE contains(tags, "#problem:classification")
SORT created DESC
```

## 💡 작성 가이드

### 좋은 노트의 조건

✅ 명확한 문제 정의  
✅ 구체적인 솔루션  
✅ Trade-off 명시  
✅ 실제 코드/예시  
✅ 관련 노트 링크  
✅ 정확한 태그

### 워크플로우

```mermaid
graph LR
    A[문제 분석] --> B[태그 결정]
    B --> C[파일명 생성]
    C --> D[문서 작성]
    D --> E[링크 연결]
    E --> F[Graph 확인]
```

## 🔧 필수 도구

### Obsidian 플러그인

- **Dataview**: 태그 기반 쿼리 및 동적 목록
- **Templater**: 템플릿 자동화
- **Graph Analysis**: 연결 관계 시각화
- **Tag Wrangler**: 태그 관리 (선택)

## 📊 현재 상태

- 총 노트 수: [자동 업데이트]
- 주요 패턴: [자동 업데이트]
- 최근 업데이트: [자동 업데이트]

```dataview
LIST
FROM "Neurons"
SORT file.mtime DESC
LIMIT 5
```

## 🤝 기여 방법

### 노트 작성 원칙

1. **학습 우선**: 자동화보다 이해가 먼저
2. **일관성**: 태그와 파일명 규칙 준수
3. **연결성**: 독립된 노트보다 연결된 네트워크
4. **실용성**: 이론보다 적용 가능한 솔루션

### 품질 체크리스트

- [ ] 태그가 내용과 일치하는가?
- [ ] 파일명이 태그를 반영하는가?
- [ ] 관련 노트와 링크되어 있는가?
- [ ] 실제 적용 가능한 코드가 있는가?
- [ ] Trade-off가 명시되어 있는가?

## 🚀 Ultimate Vision: AI-Powered ML Solution Architect

### 목표: “5분 인터뷰 → 완성된 ML 로드맵”

MercuryDB는 궁극적으로 기업의 비즈니스 컨텍스트를 분석하여 최적의 ML 솔루션을 자동으로 설계하는 시스템으로 진화합니다.

### 작동 원리

```mermaid
graph LR
    A[CEO 인터뷰] --> B[텍스트 전사]
    B --> C[키워드 추출]
    C --> D[태그 매핑]
    D --> E[패턴 매칭]
    E --> F[솔루션 생성]
    F --> G[ML 파이프라인 제안]
```

### 예시 시나리오

**입력 (인터뷰 내용):**

> “우리는 온라인 쇼핑몰을 운영하는데, 매일 수만 건의 주문이 들어와요.
> 최근에 사기 거래가 늘어서 실시간으로 이상한 거래를 잡아내고 싶습니다.”

**자동 분석:**

```yaml
추출된 태그:
- #industry:retail
- #problem:anomaly
- #constraint:real-time
- #constraint:high-volume

매칭된 패턴:
- anomaly-kinesis-sagemaker.md (90% 일치)
- fraud-detection-realtime.md (85% 일치)
- retail-fraud-pipeline.md (95% 일치)
```

**출력 (ML 솔루션):**

- 실시간 스트리밍: Kinesis Data Streams
- 이상 탐지: SageMaker Random Cut Forest
- 예상 비용: $750/월
- 구현 기간: 6주
- ROI: 3개월 내 break-even

## 📈 로드맵

### Phase 1: Foundation (현재)

- [x] 태그 시스템 구축
- [x] 파일명 규칙 확립
- [ ] 핵심 패턴 50개 문서화
- [ ] 기본 태그 매핑 사전 구축

### Phase 2: Expansion (3-6개월)

- [ ] 패턴 100개 달성
- [ ] 산업별 플레이북 완성
- [ ] 키워드-태그 매핑 자동화
- [ ] 간단한 매칭 알고리즘 구현

### Phase 3: Intelligence (6-12개월)

- [ ] 패턴 200개 이상 축적
- [ ] GPT API 통합 (텍스트 분석)
- [ ] 자동 솔루션 생성기 개발
- [ ] 비용-성능 최적화 엔진

### Phase 4: Automation (12개월+)

- [ ] 음성 인터뷰 → 자동 전사
- [ ] 실시간 솔루션 제안
- [ ] ROI 예측 모델
- [ ] 구현 로드맵 자동 생성

## 📚 참고 자료

- [AWS Documentation](https://docs.aws.amazon.com/)
- [MLS-C01 Exam Guide](https://aws.amazon.com/certification/certified-machine-learning-specialty/)
- [Obsidian Documentation](https://obsidian.md/)

## 📝 라이선스

이 프로젝트는 학습 및 지식 공유를 목적으로 합니다.

-----

**Created by**: Sehyun YU
**Started**: 2025-08-15
**Version**: 1.0.0

-----

*“Building the future of ML solution design, one neuron at a time.”*