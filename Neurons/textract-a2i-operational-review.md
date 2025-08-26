---
title: textract-a2i-operational-review
created: 2025-08-26
modified: 2025-08-26
tags:
- service/textract
- service/a2i
- constraint/operational
- usecase/document-processing
- technique/human-review
aliases: ["textract-review", "a2i-groundtruth", "operational-ai"]

---

# Amazon Textract와 A2I를 활용한 운영 중 문서 처리 품질 개선

## 🎯 핵심 포인트

운영 중인 AI 모델의 낮은 신뢰도 예측에 대해 사람의 검토가 필요한 경우, Amazon A2I를 통해 실시간 품질 보증과 처리 시간 단축을 동시에 달성할 수 있다.

## 📝 설명

### Amazon A2I가 운영 환경 품질 개선에 적합한 이유

Amazon A2I(Augmented AI)는 이미 배포된 머신러닝 모델의 예측 결과 중 신뢰도가 낮은 경우를 자동으로 감지하여 사람의 검토 워크플로로 라우팅하는 서비스입니다. 특히 Amazon Textract와 같은 문서 처리 서비스에서 OCR 품질이 불확실한 경우, 비즈니스 검증 단계에서 실패하기 전에 미리 사람이 검토할 수 있도록 합니다.

### 아키텍처 플로우

```
스캔된 문서 → Amazon Textract → 신뢰도 점수 확인
                                      ↓
                              낮은 신뢰도 감지
                                      ↓
                            Amazon A2I 워크플로 트리거
                                      ↓
                              사람 검토자에게 전달
                                      ↓
                            검토 완료 후 정확한 데이터
                                      ↓
                              비즈니스 검증 통과
                                      ↓
                            대출 신청서 자동 처리 완료
```

### Trade-offs 고려사항

**Amazon A2I 장점**:
- 실시간 운영 환경에서 즉시 품질 개선
- 비즈니스 검증 실패율 감소로 전체 처리 시간 단축
- 기존 Textract 모델 변경 없이 바로 적용 가능
- 사람의 피드백을 향후 모델 개선에 활용 가능

**Amazon A2I 단점**:
- 초기 사람 검토 단계에서 추가 시간 소요
- 사람 검토자의 가용성에 따른 처리량 제한

**Amazon SageMaker Ground Truth 장점**:
- 대규모 훈련 데이터셋 생성에 최적화
- 다양한 라벨링 작업 지원

**Amazon SageMaker Ground Truth 단점**:
- 모델 재훈련 필요로 즉각적 개선 불가
- 새로운 모델 배포까지 긴 개발 주기
- 현재 운영 중인 프로세스와 별도의 워크플로

## 🔍 주요개념

### 운영 단계별 AI 품질 관리 방식 비교

- **Ground Truth (훈련 단계)**: 원시 데이터에 라벨을 붙여 훈련 데이터셋을 생성하는 서비스. 모델을 새로 만들거나 개선할 때 사용
- **A2I (운영 단계)**: 이미 배포된 모델의 예측 결과 중 신뢰도가 낮은 경우를 사람이 검토하는 서비스. 실시간 품질 보증에 사용

### 실전 적용

- 대출 서류 처리에서 OCR 신뢰도가 80% 미만인 경우 자동으로 사람 검토 요청
- 의료 문서 처리에서 중요한 진단명이나 약물명 추출 시 높은 정확도 요구 사항
- 법률 문서에서 계약 조건이나 금액 정보 추출 시 오류 방지를 위한 이중 검증

## 📝 관련 문제

**Question:** A corporation uses Amazon Textract daily to extract textual data from thousands of scanned legal documents for automated loan application processing. Some documents fail business validation and require human review, increasing processing time. What should the organization do to expedite loan application processing?

**Options:**

- A) Configure Amazon Textract to route low-confidence predictions to Amazon SageMaker Ground Truth for manual review before business validation
- B) Use Amazon Textract synchronous operations instead of asynchronous operations
- C) Configure Amazon Textract to route low-confidence predictions to Amazon Augmented AI (A2I) for manual review before business validation
- D) Use Amazon Rekognition's text detection feature instead of Amazon Textract for document processing

**정답: C) Amazon Augmented AI (A2I)**

💡 추가 설명:

- **옵션 A (Ground Truth)** - 훈련 단계에서 사용하는 라벨링 서비스로, 이미 운영 중인 환경의 즉각적인 품질 개선에는 부적합
- **옵션 B (동기 처리)** - 처리 방식 변경으로는 문서 품질 문제 해결 불가, 오히려 처리량 제한 가능성
- **옵션 D (Rekognition)** - 일반적인 이미지 텍스트 감지 서비스로 법률 문서의 복잡한 레이아웃과 전문 용어 처리에 한계