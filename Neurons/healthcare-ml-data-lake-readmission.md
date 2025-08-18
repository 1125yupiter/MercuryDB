---

title: healthcare-ml-data-lake-readmission
created: 2025-08-18
modified: 2025-08-18
tags:
- industry/healthcare
- service/data-lake
- problem/classification
- constraint/multi-source
- usecase/readmission-prediction
aliases: ["patient-readmission", "healthcare-ml", "medical-data-integration"]

---

# 헬스케어 ML 모델을 위한 중앙집중식 데이터 레이크 전략

## 🎯 핵심 포인트

환자 재입원율 예측을 위한 머신러닝 모델 개발에서 다양한 형태의 환자 데이터를 통합해야 하는 경우, 중앙집중식 데이터 레이크를 사용하여 확장 가능하고 접근성이 높은 데이터 저장소를 구축할 수 있다.

## 📝 설명

### 데이터 레이크가 헬스케어 ML에 적합한 이유

데이터 레이크는 구조화, 반구조화, 비구조화 데이터를 모두 원시 형태로 저장할 수 있는 중앙집중식 저장소입니다. 헬스케어 환경에서는 인구통계학적 데이터(구조화), 의료 기록(반구조화/비구조화), 약물 기록(구조화), 실험실 결과(구조화/반구조화) 등 다양한 형태의 데이터를 다뤄야 합니다. 데이터 레이크는 이러한 이질적 데이터를 통합하고 ML 모델 훈련을 위한 유연한 접근을 제공합니다.

### 아키텍처 플로우

```
데이터 소스들 → 데이터 수집 레이어 → 데이터 레이크 (AWS S3)
    ↓                ↓                    ↓
의료 기록      ETL/스트리밍          Raw Data Zone
약물 기록   →   파이프라인     →    Processed Data Zone
실험실 결과      (Kinesis/Glue)      Curated Data Zone
인구통계                              ↓
                                 ML 모델 훈련
                                (SageMaker)
```

### Trade-offs 고려사항

**중앙집중식 데이터 레이크 장점**:
- 다양한 데이터 형태와 포맷 지원
- 스키마 온 리드 방식으로 유연한 데이터 구조
- 수평적 확장성과 비용 효율성
- 데이터 과학자들의 쉬운 접근성
- 실시간 및 배치 처리 모두 지원

**중앙집중식 데이터 레이크 단점**:
- 데이터 거버넌스와 품질 관리 복잡성
- 보안 및 접근 제어 설정의 중요성
- 데이터 늪(Data Swamp) 위험

**온프레미스 데이터베이스 장점**:
- 완전한 데이터 제어권
- 낮은 네트워크 지연 시간

**온프레미스 데이터베이스 단점**:
- 제한된 확장성과 높은 유지보수 비용
- 다양한 데이터 형태 처리의 어려움
- 하드웨어 투자 및 관리 부담

**FTP 서버 장점**:
- 단순한 파일 전송 메커니즘

**FTP 서버 단점**:
- 보안 취약성 (특히 민감한 의료 데이터)
- 대용량 데이터 처리 부적합
- 데이터 통합 및 분석 기능 부재

**분리된 RDS 인스턴스 장점**:
- 강력한 ACID 속성과 데이터 일관성
- 암호화 및 보안 기능

**분리된 RDS 인스턴스 단점**:
- 데이터 사일로 형성으로 통합 분석 어려움
- 복잡한 조인 연산 시 성능 저하
- 스키마 변경의 어려움

## 🔍 주요개념

### 데이터 저장 방식 비교

- **데이터 레이크**: 원시 형태로 모든 데이터 저장, 사용 시점에 스키마 적용 (Schema-on-Read)
- **데이터 웨어하우스**: 구조화된 데이터만 저장, 저장 시점에 스키마 적용 (Schema-on-Write)

### 실전 적용

- **환자 재입원 위험 예측**: 과거 입원 기록, 진단 코드, 약물 이력을 통합 분석
- **개인화된 치료 계획**: 유전자 데이터, 생활 습관, 의료 기록을 결합한 맞춤형 치료
- **의료비 예측 모델**: 보험 청구 데이터, 진료 기록, 인구통계 정보를 활용한 비용 예측

## 📝 관련 문제

**Question:** A healthcare company is planning to develop a machine learning model to predict patient readmission rates based on historical patient data. The data science team needs to create a data repository that integrates various types of patient data such as demographics, previous medical history, medication records, and lab test results. Which strategy should the data engineering team use to identify and organize the primary data sources effectively, ensuring the data is accessible and formatted suitably for training the machine learning model?

**Options:**

- A) Store data in a centralized data lake
- B) Store data in an on-premise database  
- C) Store data on FTP servers
- D) Store encrypted data in isolated AWS RDS instances

**정답: A) Store data in a centralized data lake**

💡 추가 설명:

- **B) 온프레미스 데이터베이스** - 대용량의 다양한 형태 데이터 처리에 확장성 제한, 높은 유지보수 비용
- **C) FTP 서버** - 민감한 의료 데이터에 대한 보안 취약성, 대용량 데이터 처리 및 분석 기능 부재
- **D) 분리된 RDS 인스턴스** - 데이터 사일로 형성으로 통합 분석 어려움, 복잡한 데이터 접근 및 관리