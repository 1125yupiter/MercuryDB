---

title: medical-data-sagemaker-private-workforce
created: 2025-08-27
modified: 2025-08-27
tags:
- service/sagemaker-groundtruth
- data/medical-sensitive
- workforce/private
- security/hipaa-compliance
- usecase/image-labeling
aliases: ["private-workforce", "ground-truth", "medical-labeling"]

---

# 의료 데이터 라벨링을 위한 SageMaker Ground Truth Private Workforce

## 🎯 핵심 포인트

민감한 의료 데이터를 라벨링해야 하는 경우 Amazon SageMaker Ground Truth에서 Private Workforce를 사용하여, 인증된 내부 직원만 접근 가능한 안전한 라벨링 파이프라인을 구축할 수 있다.

## 📝 설명

### SageMaker Ground Truth가 의료 데이터 라벨링에 적합한 이유

Amazon SageMaker Ground Truth는 완전 관리형 데이터 라벨링 서비스로, 의료 데이터와 같은 민감한 정보를 처리할 때 Private Workforce 옵션을 제공합니다. 이를 통해 HIPAA 규정 준수가 필요한 환경에서도 안전하게 데이터 라벨링을 수행할 수 있습니다. Built-in bounding box 태스크 타입을 사용하면 CT 스캔이나 MRI 이미지에서 관심 영역을 표시하는 작업을 효율적으로 처리할 수 있습니다.

### 아키텍처 플로우

```
S3 Bucket (CT Scans) 
    ↓
Manifest File (이미지 경로 정의)
    ↓
SageMaker Ground Truth
    ↓
Private Workforce (인증된 직원들)
    ↓
Labeling Interface (Built-in Bounding Box)
    ↓
Labeled Dataset (S3 저장)
    ↓
ML Model Training
```

### Trade-offs 고려사항

**SageMaker Ground Truth + Private Workforce 장점**:
- 완전 관리형 서비스로 인프라 관리 불필요
- Built-in 태스크 타입으로 개발 시간 단축
- Private workforce로 데이터 보안 보장
- 자동 품질 관리 및 검증 기능

**SageMaker Ground Truth + Private Workforce 단점**:
- 비용이 상대적으로 높음
- Private workforce 구성에 시간 필요
- Built-in 태스크 타입의 제한된 커스터마이징

**Amazon Mechanical Turk 장점**:
- 빠른 라벨링 속도
- 저렴한 비용
- 대규모 작업자 풀

**Amazon Mechanical Turk 단점**:
- 불특정 다수에게 데이터 노출
- 의료 데이터 보안 규정 위반
- 품질 관리의 어려움

## 🔍 주요개념

### Workforce 유형 비교

- **Private Workforce**: 조직 내 인증된 직원들로만 구성된 작업자 그룹. 민감한 데이터 처리에 적합
- **Public Workforce (Mechanical Turk)**: 전 세계 불특정 다수의 작업자. 일반적인 데이터 라벨링에 적합
- **Vendor Workforce**: 제3자 전문 라벨링 업체. 품질과 보안의 균형

### 실전 적용

- **의료 영상**: CT, MRI, X-Ray 스캔에서 병변 영역 표시
- **금융 문서**: 계약서, 대출 서류의 중요 정보 추출 및 분류
- **법률 문서**: 소송 관련 문서에서 민감한 개인정보 식별 및 마스킹

## 📝 관련 문제

**Question:** A medical imaging company wants to train a computer vision model to detect areas of concern on patients' CT scans. The company has a large collection of unlabeled CT scans that are linked to each patient and stored in an Amazon S3 bucket. The scans must be accessible to authorized users only. A machine learning engineer needs to build a labeling pipeline. Which set of steps should the engineer take to build the labeling pipeline with the LEAST effort?

**Options:**

- A) Create a workforce with AWS Identity and Access Management (IAM). Build a labeling tool on Amazon EC2. Queue images for labeling by using Amazon Simple Queue Service (Amazon SQS). Write the labeling instructions.
- B) Create an Amazon Mechanical Turk workforce and manifest file. Create a labeling job by using the built-in image classification task type in Amazon SageMaker Ground Truth. Write the labeling instructions.
- C) Create a private workforce and manifest file. Create a labeling job by using the built-in bounding box task type in Amazon SageMaker Ground Truth. Write the labeling instructions.
- D) Create a workforce with Amazon Cognito. Build a labeling web application with AWS Amplify. Build a labeling workflow backend using AWS Lambda. Write the labeling instructions.

**정답: C) Create a private workforce and manifest file. Create a labeling job by using the built-in bounding box task type in Amazon SageMaker Ground Truth**

💡 추가 설명:

- **Option A** - 모든 컴포넌트를 처음부터 개발해야 하므로 "최소 노력" 요구사항에 부적합
- **Option B** - Mechanical Turk는 공개 플랫폼으로 민감한 의료 데이터를 불특정 다수에게 노출시키는 보안 위험
- **Option D** - 웹 애플리케이션과 백엔드를 직접 개발해야 하므로 상당한 개발 노력 필요

## 🔒 보안 원칙 기억하기

**민감 데이터 = Private Workforce 필수**
- 🏥 의료: CT, MRI, 환자 기록 등
- ⚖️ 법률: 계약서, 법정 문서 등  
- 💰 금융: 신용카드 정보, 거래 기록 등
- 🔐 개인정보: 이름, 주소, SSN 등

**절대 원칙**: 민감한 데이터는 절대 Public Workforce나 Mechanical Turk 사용 금지!