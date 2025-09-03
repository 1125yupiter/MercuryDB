---

title: security-rekognition-opensearch-realtime
created: 2024-08-22
modified: 2024-08-22
tags:
- service/rekognition
- service/opensearch
- constraint/real-time
- usecase/security-monitoring
- architecture/video-analytics
aliases: ["face-recognition-security", "video-analytics", "rekognition-es"]

---

# 보안 시스템에서 Rekognition과 OpenSearch를 활용한 실시간 얼굴 인식 아키텍처

## 🎯 핵심 포인트

대규모 보안 시스템에서 실시간 비직원 감지가 필요한 경우, Kinesis Video Streams와 Rekognition Video Stream Processor를 사용하여 라이브 비디오 스트림을 분석하고, 분석 결과를 OpenSearch에 저장하여 즉시 검색 가능한 보안 데이터베이스를 구축할 수 있다.

## 📝 설명

### Rekognition Video가 실시간 보안 시스템에 적합한 이유

Amazon Rekognition Video는 라이브 비디오 스트림에서 실시간 얼굴 인식을 수행할 수 있는 완전 관리형 서비스입니다. Stream Processor 기능을 통해 Kinesis Video Streams로부터 연속적인 비디오 데이터를 받아 즉시 분석하며, 사전 등록된 얼굴 컬렉션과 비교하여 알려진 직원과 미확인 인물을 구분할 수 있습니다.

### 아키텍처 플로우

```
카메라 (RTSP) → 프록시 서버 → Kinesis Video Streams
                                        ↓
알림 시스템 ← Rekognition Video Stream Processor
                                        ↓
OpenSearch ← Lambda 함수 ← 분석 결과 (얼굴 메타데이터)
    ↓
실시간 대시보드 (Kibana/OpenSearch Dashboards)
```

### 데이터 저장 전략

**S3**: 원본 이미지/비디오 파일 저장
```
s3://security-bucket/2024/08/22/CAM_001_143015.jpg
```

**OpenSearch**: 검색 가능한 메타데이터 저장
```json
{
  "timestamp": "2024-08-22T14:30:15Z",
  "camera_id": "CAM_FL3_LOBBY_001",
  "face_detected": true,
  "employee_match": false,
  "confidence": 0.94,
  "s3_location": "s3://security-bucket/2024/08/22/...",
  "location": {"floor": 3, "zone": "lobby"}
}
```

### Trade-offs 고려사항

**Rekognition Video 장점**:
- 실시간 스트림 처리에 최적화
- Stream Processor로 연속적인 얼굴 인식 가능
- 사전 등록된 얼굴 컬렉션과 자동 비교
- 확장성 (수백 개 카메라 동시 처리)

**Rekognition Video 단점**:
- 비용이 상대적으로 높음 (스트림당 과금)
- 네트워크 대역폭 요구사항
- 라이브 스트림 중단 시 분석 불가

**Rekognition Image 장점**:
- 정적 이미지 분석에는 정확도 높음
- 비용 효율적 (이미지당 과금)

**Rekognition Image 단점**:
- 실시간 비디오 스트림에 부적합
- 프레임별 개별 분석 필요 (지연 발생)
- 연속성 있는 추적 불가

## 🔍 주요개념

### OpenSearch vs 일반 데이터베이스

- **OpenSearch**: 검색 엔진 + 데이터베이스. 복잡한 텍스트 검색, 유사도 검색, 실시간 분석에 최적화
- **일반 DB (MySQL)**: 정확한 일치 검색, 트랜잭션 처리에 최적화

### 데이터 저장 분리 전략

- **이미지/비디오 원본**: S3 (저렴한 대용량 저장)
- **검색용 메타데이터**: OpenSearch (빠른 검색)
- **사용자/권한 정보**: RDS (정형 데이터)

### 실전 적용

- **기업 보안**: 퇴근 후 미승인 접근 감지, VIP 방문자 자동 인식
- **소매업**: 블랙리스트 고객 자동 감지, 매장 내 고객 동선 분석
- **공공시설**: 출입 통제구역 관리, 수배자 자동 감지

## 📝 관련 문제

**Question:** A commercial security firm successfully piloted 100 cameras and now wants to expand to hundreds of video cameras across worldwide offices for real-time non-employee detection. Which architecture should they consider?

**Options:**

- A) Use proxy servers at each office, stream RTSP to Kinesis Video Streams, use Rekognition Video with stream processors to detect faces from known employee collections
- B) Use proxy servers at each office, stream RTSP to Kinesis Video Streams, use Rekognition Image to detect faces from employee collections  
- C) Install AWS DeepLens cameras, use DeepLens_Kinesis_Video module, use Rekognition Video with stream processors
- D) Install AWS DeepLens cameras, use DeepLens_Kinesis_Video module, use Lambda with Rekognition Image
- E) Store all videos in S3, use Lambda to periodically analyze with Rekognition Image

**정답: A) Rekognition Video with Stream Processors**

💡 추가 설명:

- **옵션 B (Rekognition Image)** - 정적 이미지 분석용으로 실시간 비디오 스트림에는 부적합, 지연 발생
- **옵션 C,D (DeepLens)** - 개발자 실험용 제품으로 상용 운영 환경에는 사용 불가 (AWS 이용약관 제한)
- **옵션 E (배치 처리)** - 실시간 요구사항을 만족하지 않음, 주기적 분석으로 즉시성 부족

---