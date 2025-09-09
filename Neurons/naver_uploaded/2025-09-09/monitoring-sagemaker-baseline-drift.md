---

title: monitoring-sagemaker-baseline-drift
created: 2025-08-26
modified: 2025-08-26
tags:
- service/sagemaker-monitoring
- problem/model-drift
- technique/baseline-update
- constraint/production
- usecase/model-violations
aliases: ["model-monitor-baseline", "drift-baseline", "monitoring-violations"]

---

# SageMaker Model Monitor에서 모델 재훈련 후 지속적인 위반 해결

## 🎯 핵심 포인트

모델 드리프트 발생으로 새로운 데이터로 모델을 재훈련한 경우에도 위반이 지속된다면, 기존 baseline을 새로운 훈련 데이터셋으로 재생성해야 한다.

## 📝 설명

### SageMaker Model Monitor가 위반을 지속적으로 감지하는 이유

SageMaker Model Monitor는 프로덕션 환경에서 모델의 성능과 데이터 품질을 모니터링하는 서비스입니다. 위반(violations)이 감지되면 일반적으로 모델을 재훈련하지만, 단순히 모델만 업데이트하는 것으로는 해결되지 않는 경우가 있습니다.

**핵심 문제**: 새로운 데이터로 훈련된 모델을 기존 baseline으로 평가하는 **baseline 불일치**

### 문제 발생 시나리오

```
Step 1: 기존 데이터A → 모델A + Baseline A 생성
Step 2: 프로덕션 운영 → Baseline A로 모니터링 ✓

Step 3: 데이터 드리프트 발생 → 위반 감지!
Step 4: 새로운 데이터B → 모델B 재훈련 ✓
Step 5: 모델B 배포 → 여전히 Baseline A로 평가 ✗

결과: 새 모델의 정상 출력이 기존 baseline 기준으로는 "위반"
```

### Baseline의 역할과 구성 요소

**Baseline**은 모델이 정상 작동할 때의 통계적 특성을 기록한 기준선입니다:

- **입력 데이터 통계**: 평균, 분산, 분포, 결측값 비율
- **모델 출력 통계**: 예측값 범위, 확률 분포, 클래스별 비율
- **성능 지표**: 정확도, 정밀도, 재현율 등의 임계값

### 아키텍처 플로우

```
[데이터 드리프트 감지]
        ↓
[새 데이터로 모델 재훈련] ← 전문가가 완료한 부분
        ↓
[새 모델 배포]
        ↓
[기존 Baseline으로 평가] ← 문제 지점!
        ↓
[지속적인 위반 발생]
        ↓
[새 데이터로 Baseline 재생성] ← 해결책
        ↓
[새 Baseline으로 모니터링]
        ↓
[정상 운영]
```

### Trade-offs 고려사항

**Baseline 재생성의 장점**:
- 모델과 모니터링 기준의 일관성 확보
- 현재 데이터 패턴에 맞는 정확한 위반 감지
- 불필요한 알람 제거로 운영 효율성 증대

**Baseline 재생성의 단점**:
- 추가 작업 시간 및 리소스 필요
- 새로운 baseline 검증 과정 필요
- 일시적인 모니터링 공백 가능성

**다른 해결책들의 한계**:
- **수동 모니터링 재실행**: 근본 원인 해결 안됨
- **엔드포인트 재생성**: 기술적 문제가 아니므로 효과 없음
- **데이터 혼합 재훈련**: 불필요한 복잡성, baseline 문제 지속

## 🔍 주요개념

### 위반(Violations) 유형별 분류

- **Data Drift**: 입력 데이터 분포 변화 (나이 평균 30세 → 45세)
- **Model Quality**: 모델 성능 지표 저하 (정확도 95% → 80%)
- **Statistical Constraints**: 통계적 임계값 초과 (결측값 5% 초과)
- **Data Quality**: 데이터 형식/범위 문제 (음수 나이값 입력)

### 실전 적용 시나리오

- **금융 서비스**: 경제 상황 변화로 고객 신용 패턴이 달라진 경우
- **이커머스**: 계절성 변화나 마케팅 캠페인으로 구매 패턴이 변한 경우  
- **헬스케어**: 새로운 질병 유행이나 치료법 도입으로 데이터 분포가 변한 경우

## 📝 관련 문제

**Question:** A machine learning professional is configuring model monitoring on a production Amazon SageMaker endpoint. When the Amazon SageMaker Model Monitor identifies violations on the SageMaker endpoint, the professional retrains the model using the most recent dataset that is statistically typical of production traffic at the time. The professional sees that the SageMaker endpoint continues to have violations even after deploying the new model and running the first monitoring job. What actions should the professional take to resolve the violations?

**Options:**

- A) Manually trigger the monitoring job to re-evaluate the SageMaker endpoint traffic sample
- B) Run the Model Monitor baseline job again on the new training set. Configure Model Monitor to use the new baseline
- C) Delete the endpoint and recreate it with the original configuration  
- D) Retrain the model again by using a combination of the original training set and the new training set

**정답: B) Run the Model Monitor baseline job again on the new training set. Configure Model Monitor to use the new baseline**

💡 추가 설명:

- **A) 수동 모니터링 재실행** - 근본적인 baseline 불일치 문제를 해결하지 못함
- **C) 엔드포인트 재생성** - 설정 문제가 아닌 baseline 문제이므로 효과 없음  
- **D) 데이터셋 혼합 재훈련** - 불필요한 복잡성 추가, baseline 문제는 여전히 남음

---

*핵심: 모델이 진화하면 평가 기준(baseline)도 함께 진화해야 한다*