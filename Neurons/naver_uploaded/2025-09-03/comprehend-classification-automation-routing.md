---

title: comprehend-classification-automation-routing
created: 2025-08-23
modified: 2025-08-23
tags:
- service/comprehend
- problem/classification
- constraint/no-ml-expertise
- usecase/claim-routing
- deployment/managed-service
aliases: ["comprehend-custom", "claim-automation", "text-classification"]

---

# Amazon Comprehend Custom Classification을 활용한 자동화된 클레임 라우팅 시스템

## 🎯 핵심 포인트

ML 전문지식이 없는 소규모 개발팀이 기존 라벨링된 클레임 데이터를 활용하여 자동 분류 시스템을 구축하는 경우, Amazon Comprehend Custom Classification에서 완전 관리형 서비스로 복잡성을 추상화하여 간단한 API 통합만으로 텍스트 분류를 구현할 수 있다.

## 📝 설명

### Amazon Comprehend Custom Classification이 클레임 자동 분류에 적합한 이유

Amazon Comprehend Custom Classification은 AWS의 완전 관리형 자연어 처리 서비스로, 기업이 자체 데이터셋을 사용하여 맞춤형 텍스트 분류 모델을 구축할 수 있게 해준다. 이 서비스의 핵심 장점은 복잡한 ML 파이프라인을 완전히 추상화하여 개발자가 데이터 준비, 모델 훈련, 하이퍼파라미터 튜닝, 인프라 관리 등에 대한 전문 지식 없이도 고품질 분류 모델을 구축할 수 있다는 점이다.

기존 데이터베이스에 저장된 클레임 카테고리 정보를 활용하여 CSV 형태로 내보낸 후, claim_label과 claim_text 두 컬럼으로 구성된 훈련 데이터를 Comprehend에 업로드하면 자동으로 모델 훈련이 진행된다. 훈련 완료 후에는 간단한 API 호출을 통해 새로운 클레임 텍스트에 대한 카테고리 예측을 수행할 수 있다.

### 아키텍처 플로우

```
기존 데이터베이스 → CSV 내보내기 (claim_label, claim_text)
        ↓
Amazon Comprehend Custom Classification 모델 훈련
        ↓
새로운 클레임 입력 → Comprehend API 호출 → 카테고리 예측
        ↓
자동 큐 라우팅 → 우선순위 처리
```

### Trade-offs 고려사항

**Amazon Comprehend 장점**:
- ML 전문지식 불요 - 완전 관리형 서비스
- 기존 라벨링된 데이터 직접 활용 가능
- 간단한 CSV 업로드로 모델 훈련
- RESTful API를 통한 쉬운 통합
- AWS 인프라의 확장성과 안정성
- 자동 하이퍼파라미터 최적화

**Amazon Comprehend 단점**:
- AWS 종속성으로 인한 벤더 락인
- 커스터마이징 옵션이 제한적
- 사용량에 따른 API 호출 비용

**SageMaker Object2Vec 장점**:
- 더 많은 커스터마이징 옵션
- 다양한 ML 알고리즘 선택 가능

**SageMaker Object2Vec 단점**:
- 상당한 ML 전문지식 필요
- 모델 배포 및 관리 복잡성
- 하이퍼파라미터 튜닝 등 수동 작업 필요

**SageMaker LDA 장점**:
- 토픽 자동 발견 가능
- 비지도 학습으로 새로운 패턴 식별

**SageMaker LDA 단점**:
- 기존 라벨 데이터 활용 불가
- 결과 해석에 ML 전문지식 필요
- 비즈니스 카테고리와 매핑 복잡성

**Textract 조합 단점**:
- 데이터베이스 처리에 불필요한 복잡성
- Textract는 문서/이미지 텍스트 추출용
- 구조화된 데이터에는 부적합

## 🔍 주요개념

### 관리형 서비스 vs 셀프 매니지드 솔루션

- **Amazon Comprehend**: AWS가 모든 ML 파이프라인을 관리하는 완전 관리형 서비스로, 사용자는 데이터 준비와 API 통합에만 집중
- **Amazon SageMaker**: 더 많은 제어권을 제공하지만 모델 개발, 훈련, 배포, 모니터링 등 전체 ML 생명주기 관리 필요

### 실전 적용

- **고객 지원 센터**: 이메일, 채팅 문의를 자동으로 기술지원, 결제문의, 제품문의 등으로 분류하여 적절한 팀에 라우팅
- **보험 클레임 처리**: 자동차, 건강, 재산 보험 클레임을 자동 분류하여 전문 심사팀에 우선순위 배정
- **법무팀 문서 관리**: 계약서, 소송, 규정준수 관련 문서를 자동 분류하여 담당 변호사에게 할당

## 📝 관련 문제

**Question:** A retailer wishes to modernize its customer assistance system. The organization wants to automate the routing of client claims to distinct queues in order to prioritize them by type. Currently, category assignment and routing are performed manually by an operator. After classifying and routing the claim, the operator enters the information into a central database. The claim's record contains information on the claim's category. The firm lacks a data science staff and expertise in machine learning (ML). The company's small development staff needs a solution that does not require machine learning knowledge. Which solution satisfies these criteria?

**Options:**

- A) Export the database to a .csv file with two columns: claim_label and claim_text. Use the Amazon SageMaker Object2Vec algorithm and the .csv file to train a model. Use SageMaker to deploy the model to an inference endpoint. Develop a service in the application to use the inference endpoint to process incoming claims, predict the labels, and route the claims to the appropriate queue.
- B) Export the database to a .csv file with one column: claim_text. Use the Amazon SageMaker Latent Dirichlet Allocation (LDA) algorithm and the .csv file to train a model. Use the LDA algorithm to detect labels automatically. Use SageMaker to deploy the model to an inference endpoint. Develop a service in the application to use the inference endpoint to process incoming claims, predict the labels, and route the claims to the appropriate queue.
- C) Use Amazon Textract to process the database and automatically detect two columns: claim_label and claim_text. Use Amazon Comprehend custom classification and the extracted information to train the custom classifier. Develop a service in the application to use the Amazon Comprehend API to process incoming claims, predict the labels, and route the claims to the appropriate queue.
- D) Export the database to a .csv file with two columns: claim_label and claim_text. Use Amazon Comprehend custom classification and the .csv file to train the custom classifier. Develop a service in the application to use the Amazon Comprehend API to process incoming claims, predict the labels, and route the claims to the appropriate queue.

**정답: D) Amazon Comprehend Custom Classification**

💡 추가 설명:

- **Option A (SageMaker Object2Vec)** - 상당한 ML 전문지식이 필요하며, 모델 구성, 하이퍼파라미터 튜닝, 추론 엔드포인트 관리 등 복잡한 작업이 요구됨
- **Option B (SageMaker LDA)** - 비지도 학습 방식으로 기존 라벨 데이터를 활용하지 못하고, 결과 해석에 ML 전문지식이 필요함
- **Option C (Textract 조합)** - Textract는 문서/이미지에서 텍스트를 추출하는 서비스로, 구조화된 데이터베이스 처리에는 부적합하고 불필요한 복잡성을 추가함

---