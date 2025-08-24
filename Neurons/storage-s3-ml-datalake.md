---

title: storage-s3-ml-datalake
created: 2025-08-24
modified: 2025-08-24
tags:
- service/s3
- usecase/machine-learning
- constraint/high-volume
- storage/object-storage
- architecture/data-lake
aliases: ["s3-ml", "data-lake", "storage-comparison"]

---

# AWS 저장소 선택 - 대용량 ML 시각 데이터에 적합한 아키텍처

## 🎯 핵심 포인트

대용량 시각 데이터(일일 100GB+)를 머신러닝 분석에 활용하는 경우, S3 기반 데이터 레이크에서 확장성과 비용 효율성을 모두 확보할 수 있다.

## 📝 설명

### S3 데이터 레이크가 대용량 ML 데이터에 적합한 이유

S3는 객체 스토리지로 설계되어 페타바이트급 비구조화 데이터를 저렴하게 저장할 수 있습니다. 특히 이미지, 비디오 같은 시각 데이터는 파일 크기가 크고 구조화되지 않아 S3의 특성과 완벽히 맞습니다. 

AWS ML 생태계(SageMaker, EMR, Athena, Glue)와의 네이티브 통합으로 데이터 파이프라인 구축이 용이하며, 다양한 스토리지 클래스를 통해 접근 빈도에 따른 비용 최적화가 가능합니다.

### 아키텍차 플로우

```
글로벌 트럭 플릿 → 실시간 이미지 업로드 → S3 데이터 레이크
                                           ↓
IAM 정책 + 버킷 정책 → 접근 제어 → 승인된 사용자만 접근
                                           ↓
원시 데이터(Raw) → 처리된 데이터(Processed) → ML 모델 학습
     ↓                    ↓                      ↓
  S3 Standard      S3 Standard-IA         SageMaker
```

### Trade-offs 고려사항

**S3 장점**:
- 무제한 확장성 (페타바이트급)
- 저렴한 스토리지 비용 ($0.023/GB/월)
- ML 도구와 완벽 통합
- 다양한 스토리지 클래스로 비용 최적화
- 글로벌 접근 가능

**S3 단점**:
- 실시간 쿼리 성능 한계
- 파일 수정 불가 (덮어쓰기만 가능)
- 일관성 모델의 복잡성

**DynamoDB 장점**:
- 한 자리수 밀리초 응답시간
- 자동 확장
- 완전 관리형 서비스

**DynamoDB 단점**:
- 높은 스토리지 비용 ($0.25/GB/월, S3 대비 11배)
- 대용량 바이너리 데이터에 비효율적
- 25MB 아이템 크기 제한
- ML 도구 직접 통합 제한

## 🔍 주요개념

### 저장소 유형별 특성 비교

- **객체 스토리지 (S3)**: 대용량 비구조화 데이터, 무제한 확장, 저렴한 비용
- **NoSQL DB (DynamoDB)**: 구조화된 작은 데이터, 빠른 응답, 실시간 애플리케이션
- **분산 파일시스템 (HDFS)**: 임시 대용량 처리, 고성능 병렬 처리, 일시적 저장
- **네트워크 파일시스템 (EFS)**: EC2 인스턴스 간 파일 공유, POSIX 호환

### 실전 적용

- **전자상거래**: 상품 이미지는 S3, 상품 메타데이터는 DynamoDB
- **의료 영상**: DICOM 이미지는 S3, 환자 정보는 RDS/DynamoDB  
- **소셜 미디어**: 동영상 파일은 S3, 사용자 활동 로그는 DynamoDB

## 📝 관련 문제

**Question:** A trucking business is gathering real-time visual data from its global fleet of vehicles. The data is expanding at a breakneck pace, with around 100 GB of new data created daily. The organization wishes to investigate possible applications of machine learning while guaranteeing that the data is only available to authorized IAM users. Which storage choice offers the most processing flexibility and supports IAM access control?

**Options:**

- A) Use a database, such as Amazon DynamoDB, to store the images, and set the IAM policies to restrict access to only the desired IAM users.
- B) Use an Amazon S3-backed data lake to store the raw images, and set up the permissions using bucket policies.
- C) Setup up Amazon EMR with Hadoop Distributed File System (HDFS) to store the files, and restrict access to the EMR instances using IAM policies.
- D) Configure Amazon EFS with IAM policies to make the data available to Amazon EC2 instances owned by the IAM users.

**정답: B) Amazon S3-backed data lake**

💡 추가 설명:

- **Option A (DynamoDB)** - 대용량 바이너리 데이터 저장 시 비용이 S3 대비 약 11배 높음, 이미지 저장용으로 설계되지 않음
- **Option C (EMR/HDFS)** - HDFS는 일시적 저장소로 클러스터 종료 시 데이터 손실, 24/7 운영 시 높은 비용
- **Option D (EFS)** - 단일 리전 제한으로 글로벌 플릿에 부적절, ML 도구와의 직접 통합 제한적