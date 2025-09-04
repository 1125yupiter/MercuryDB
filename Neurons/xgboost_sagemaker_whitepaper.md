# Amazon SageMaker XGBoost 구현 모드 분석 백서

## 목차
1. [개요](#개요)
2. [SageMaker XGBoost 구현 모드](#sagemaker-xgboost-구현-모드)
3. [Built-in Algorithm Mode 상세 분석](#built-in-algorithm-mode-상세-분석)
4. [Script Mode (Framework Mode) 상세 분석](#script-mode-framework-mode-상세-분석)
5. [컨테이너 기반 아키텍처](#컨테이너-기반-아키텍처)
6. [사용자 유형별 권장사항](#사용자-유형별-권장사항)
7. [결론 및 권장사항](#결론-및-권장사항)

---

## 개요

Amazon SageMaker는 머신러닝 모델의 구축, 훈련, 배포를 위한 완전 관리형 서비스로, XGBoost 알고리즘을 두 가지 구현 모드로 제공합니다. 본 백서는 Built-in Algorithm Mode와 Script Mode의 특징, 장단점, 그리고 적절한 사용 시나리오를 상세히 분석합니다.

## SageMaker XGBoost 구현 모드

### 2.1 모드 개요
SageMaker XGBoost는 다음 두 가지 방식으로 구현할 수 있습니다:

- **Built-in Algorithm Mode**: AWS에서 제공하는 사전 구축된 XGBoost 알고리즘
- **Script Mode (Framework Mode)**: 사용자 정의 XGBoost 스크립트 실행

### 2.2 모드 선택 기준
```
경량 사용자 → Built-in Algorithm Mode
헤비 사용자 → Script Mode (Framework Mode)
```

## Built-in Algorithm Mode 상세 분석

### 3.1 특징
Built-in Algorithm Mode는 AWS에서 미리 구축한 XGBoost 알고리즘 컨테이너를 사용하는 방식입니다.

### 3.2 장점 (Pros)
- **빠른 배포**: 복잡한 설정 없이 즉시 사용 가능
- **안정성**: AWS에서 검증된 알고리즘
- **간편함**: 최소한의 설정으로 훈련 시작

### 3.3 단점 (Cons)
#### 3.3.1 제한된 커스터마이제이션 (Limited Customization)
- 알고리즘 로직 수정 불가
- 사전 정의된 하이퍼파라미터만 사용

#### 3.3.2 버전 제약 (Version Restrictions)  
- AWS에서 지원하는 XGBoost 버전으로 제한
- 최신 XGBoost 기능 즉시 사용 불가

#### 3.3.3 커스텀 전처리 불가 (No Custom Preprocessing)
Built-in Mode에서는 다음과 같은 커스텀 전처리가 **불가능**합니다:

**제한되는 전처리 옵션:**
- **피처 엔지니어링**: 새로운 피처 생성, 다항식 항, 상호작용 변수 생성 불가
- **커스텀 스케일링/정규화**: 기본 표준화만 가능, 커스텀 스케일링 함수 사용 불가  
- **텍스트 처리**: 커스텀 토큰화, 임베딩, NLP 변환 불가
- **시계열 피처**: 지연 피처, 롤링 윈도우, 계절성 구성요소 생성 불가
- **도메인별 변환**: 커스텀 비즈니스 로직 변환 불가
- **데이터 정제**: 아웃라이어, 결측값, 데이터 품질 이슈의 제한적 커스텀 처리

**사용 가능한 전처리:**
- 기본 결측값 대치
- 표준 범주형 인코딩  
- 단순 수치형 스케일링
- 사전 정의된 데이터 형식 (CSV, libsvm, Parquet)

#### 3.3.4 고정된 입력 형식 (Fixed Input Formats)
- CSV, libsvm, Parquet 등 제한된 형식만 지원
- 커스텀 데이터 로더 사용 불가

### 3.4 전처리 해결 방안
Built-in Mode에서 커스텀 전처리가 필요한 경우:

1. **외부 전처리**: SageMaker 업로드 전 로컬/외부에서 데이터 전처리
2. **SageMaker Processing Jobs**: 별도의 전처리 단계 실행
3. **Script Mode 전환**: 완전한 전처리 제어를 위한 모드 변경

### 3.5 사용 프로세스
Built-in Mode는 다음과 같이 구조화되어 있습니다:

```
1. 훈련 데이터 위치 지정 (S3)
2. 하이퍼파라미터 설정
3. 훈련 시작
```

알고리즘은 사전 포맷된 깨끗한 데이터를 기대하며, 훈련 프로세스만 처리합니다.

## Script Mode (Framework Mode) 상세 분석

### 4.1 특징
Script Mode는 사용자가 작성한 XGBoost 스크립트를 SageMaker 환경에서 실행하는 방식입니다.

### 4.2 장점
- **완전한 제어권**: 전체 훈련 파이프라인 커스터마이제이션
- **유연한 전처리**: 훈련 스크립트 내에서 직접 전처리 가능
- **최신 기능**: 최신 XGBoost 버전 및 기능 활용
- **이식성**: 다른 플랫폼에서도 동일한 코드 실행 가능

### 4.3 전처리 자동화
Script Mode에서는 다음과 같은 방식으로 전처리를 자동화할 수 있습니다:

- **Script Mode**: 훈련 스크립트에 직접 전처리 코드 포함
- **커스텀 컨테이너**: Docker 이미지에 전처리 로직 내장
- **SageMaker Pipelines**: 전처리와 훈련 단계를 자동으로 연결
- **실시간 추론**: 모델 서빙 코드에 전처리 포함

### 4.4 플랫폼 호환성
- **Built-in Mode**: AWS SageMaker 전용
- **Script Mode**: 모든 플랫폼 (로컬, 클라우드 제공업체, 온프레미스) 지원

## 컨테이너 기반 아키텍처

### 5.1 현대 ML 훈련의 표준
컨테이너화는 현재 클라우드 플랫폼에서 ML 훈련의 표준 접근법입니다. 이는 다음과 같은 이점을 제공합니다:
- 일관성 (Consistency)
- 확장성 (Scalability)  
- 격리 (Isolation)

### 5.2 컨테이너 선택 방식
모든 SageMaker 훈련은 컨테이너 선택으로 시작됩니다:

```
Built-in 원할 때 → Built-in Algorithm 컨테이너 선택
커스텀 스크립트 + 헤비 유저 → Framework 컨테이너 선택
```

### 5.3 컨테이너 명명 규칙 및 실제 예시

#### 5.3.1 Built-in Algorithm 컨테이너
**명명 패턴**: `{account}.dkr.ecr.{region}.amazonaws.com/{algorithm}:{version}`

**실제 예시:**
```
382416733822.dkr.ecr.us-west-2.amazonaws.com/xgboost:1.5-1
246618743249.dkr.ecr.us-west-2.amazonaws.com/linear-learner:1
382416733822.dkr.ecr.us-east-1.amazonaws.com/kmeans:1
246618743249.dkr.ecr.us-west-2.amazonaws.com/pca:1
```

#### 5.3.2 Framework 컨테이너  
**명명 패턴**: `{account}.dkr.ecr.{region}.amazonaws.com/sagemaker-{framework}:{version}`

**실제 예시:**
```
246618743249.dkr.ecr.us-west-2.amazonaws.com/sagemaker-xgboost:1.7-1
763104351884.dkr.ecr.us-west-2.amazonaws.com/sagemaker-pytorch:1.12.0-gpu-py38
763104351884.dkr.ecr.us-west-2.amazonaws.com/sagemaker-tensorflow:2.8.0-cpu-py39
246618743249.dkr.ecr.us-west-2.amazonaws.com/sagemaker-scikit-learn:0.23-1
```

#### 5.3.3 구분 방법
- **Built-in**: 알고리즘 이름만 포함 (예: `xgboost:1.5-1`)
- **Framework**: `sagemaker-` 접두사 포함 (예: `sagemaker-xgboost:1.7-1`)

#### 5.3.4 버전 태그
- `:1` = 알고리즘/프레임워크의 버전 1
- `:1.5-1` = XGBoost 1.5 버전, SageMaker 빌드 1
- `:latest` = 최신 버전 (프로덕션에서 권장하지 않음)

### 5.4 다중 환경 지원
Built-in Mode와 Script Mode 모두 다음 환경에서 사용 가능:
- Jupyter Notebooks
- SageMaker Studio
- AWS CLI
- AWS SDK (boto3)
- SageMaker Console UI
- API

## 사용자 유형별 권장사항

### 6.1 경량 사용자 (Light Users)
**추천**: Built-in Algorithm Mode

**특징:**
- 빠른 프로토타이핑 필요
- 표준 XGBoost 기능으로 충분
- 최소한의 설정 선호
- 전처리가 단순하거나 외부에서 처리 가능

**사용 시나리오:**
- 개념 증명 (PoC) 프로젝트
- 교육 및 학습 목적
- 표준 ML 파이프라인

### 6.2 헤비 사용자 (Heavy Users)  
**추천**: Script Mode (Framework Mode)

**특징:**
- 복잡한 전처리 요구사항
- 커스텀 피처 엔지니어링 필요
- 최신 XGBoost 기능 활용
- 완전한 제어권 요구

**사용 시나리오:**
- 프로덕션 ML 파이프라인
- 연구 및 실험
- 복잡한 도메인별 요구사항
- 멀티 플랫폼 배포

## 결론 및 권장사항

### 7.1 핵심 결론

1. **모드 선택은 요구사항에 따라 결정**: 단순함 vs 유연성의 트레이드오프
2. **전처리 요구사항이 핵심 판단 기준**: Built-in Mode의 가장 큰 제약
3. **컨테이너 기반 아키텍처는 현대 ML의 표준**: 일관성과 확장성 제공
4. **점진적 접근 권장**: Built-in Mode로 시작하여 필요시 Script Mode로 전환

### 7.2 실무 권장사항

#### 7.2.1 프로젝트 초기 단계
- Built-in Mode로 빠른 베이스라인 구축
- 데이터 이해 및 기본 성능 평가

#### 7.2.2 프로덕션 준비 단계  
- 전처리 요구사항 상세 분석
- Script Mode 전환 검토
- 성능 및 비용 최적화

#### 7.2.3 장기 운영 단계
- 모니터링 및 재훈련 파이프라인 구축  
- 버전 관리 및 롤백 전략 수립

### 7.3 미래 고려사항

클라우드 기반 ML 플랫폼의 발전과 함께 컨테이너화된 접근법은 계속해서 표준이 될 것으로 예상됩니다. 조직은 초기에는 단순한 접근법(Built-in Mode)을 선택하더라도, 성숙도가 높아짐에 따라 더 유연한 접근법(Script Mode)으로의 전환을 고려해야 합니다.

---

**작성일**: 2025년 9월  
**버전**: 1.0  
**대상**: ML 엔지니어, 데이터 사이언티스트, 클라우드 아키텍트

---

## 해시태그

#AWS사용법 #SageMaker활용 #XGBoost모델링 #머신러닝파이프라인 #클라우드ML #컨테이너기반훈련 #데이터전처리 #MLOps구축 #인공지능개발 #AWS머신러닝