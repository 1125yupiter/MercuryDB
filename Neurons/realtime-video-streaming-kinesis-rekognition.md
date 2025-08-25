---

title: realtime-video-streaming-kinesis-rekognition
created: 2025-08-25
modified: 2025-08-25
tags:
- service/kinesis-video-streams
- service/rekognition-video
- constraint/real-time
- usecase/security-monitoring
- deployment/proxy-architecture
aliases: ["video-streaming", "face-detection", "rtsp-proxy"]

---

# AWS 실시간 비디오 스트리밍을 통한 보안 모니터링 시스템

## 🎯 핵심 포인트

전 세계 수백 대의 보안 카메라에서 실시간 비직원 감지가 필요한 경우, 각 지사의 프록시 서버에서 RTSP 스트림을 Kinesis Video Streams로 전송하고 Rekognition Video 스트림 프로세서로 실시간 얼굴 인식을 수행할 수 있다.

## 📝 설명

### Kinesis Video Streams + Rekognition Video가 대규모 실시간 보안 모니터링에 적합한 이유

Amazon Kinesis Video Streams는 수백 대의 카메라에서 생성되는 연속적인 비디오 데이터를 실시간으로 수집, 저장, 처리할 수 있는 완전관리형 서비스입니다. 각 카메라의 RTSP(Real-Time Streaming Protocol) 피드를 독립적인 비디오 스트림으로 처리하며, Rekognition Video의 스트림 프로세서와 통합되어 지연 시간 없는 얼굴 인식을 제공합니다.

프록시 서버 아키텍처는 각 지사에서 로컬 카메라들의 스트림을 수집하고 최적화하여 AWS로 전송함으로써 대역폭 사용량을 크게 절약하고 네트워크 안정성을 향상시킵니다. 이는 글로벌 확장 시 운영비용과 복잡성을 대폭 줄이는 핵심 요소입니다.

### 아키텍처 플로우

```
[지사별 카메라들] → [로컬 프록시 서버] → [Kinesis Video Streams] → [Rekognition Video 스트림 프로세서] → [실시간 알림]

카메라1(RTSP) ──┐
카메라2(RTSP) ──┤
카메라3(RTSP) ──┼── [프록시 서버] ── [최적화된 스트림] ── [Kinesis Video Streams]
카메라4(RTSP) ──┤                                                        │
...          ──┘                                                        ▼
                                                              [Rekognition Video]
                                                                        │
                                                                        ▼
                                                              [얼굴 컬렉션과 비교]
                                                                        │
                                                                        ▼
                                                              [비직원 감지 시 알림]
```

### Trade-offs 고려사항

**Kinesis Video Streams + Rekognition Video 장점**:
- 실시간 연속 스트림 처리로 즉각적인 위협 감지
- 자동 확장으로 수백 대 카메라 동시 처리 가능
- 스트림 프로세서의 자동화된 얼굴 인식
- 프록시 서버를 통한 효율적인 대역폭 관리

**Kinesis Video Streams + Rekognition Video 단점**:
- 연속 스트리밍으로 인한 상대적으로 높은 데이터 전송 비용
- 24/7 실시간 처리에 따른 컴퓨팅 리소스 지속 사용

**Rekognition Image 기반 솔루션 장점**:
- 개별 이미지 처리로 정밀한 분석 가능
- 필요시에만 처리하여 비용 절약 가능

**Rekognition Image 기반 솔루션 단점**:
- 프레임 단위 처리로 실시간 연속 모니터링 부적합
- Lambda 기반 처리 시 지연 시간 증가로 실시간성 저하
- 스트림 연속성이 없어 움직임 패턴 분석 제한

## 🔍 주요개념

### 핵심 기술 비교

- **RTSP (Real-Time Streaming Protocol)**: IP 카메라의 표준 실시간 비디오 스트리밍 프로토콜로, 연속적인 비디오 데이터를 네트워크를 통해 전송
- **프록시 서버**: 여러 카메라의 RTSP 스트림을 로컬에서 수집하고 최적화하여 클라우드로 중계하는 중간 서버
- **Kinesis Video Streams**: AWS의 완전관리형 비디오 스트리밍 서비스로 실시간 비디오 데이터 수집 및 저장
- **Rekognition Video 스트림 프로세서**: 비디오 스트림에서 실시간으로 얼굴을 감지하고 기존 컬렉션과 비교하는 서비스

### 실전 적용

- **글로벌 기업 본사/지사 보안**: 각 지사의 프록시 서버가 해당 지역 카메라들을 관리하고 중앙 AWS로 스트림 전송
- **대형 쇼핑몰/공항 보안**: 구역별 프록시 서버를 통해 수백 개 매장이나 게이트의 카메라를 통합 모니터링
- **제조업체 공장 보안**: 생산라인별 카메라 클러스터를 프록시 서버로 집약하여 실시간 출입 통제 및 안전 관리

## 📝 관련 문제

**Question:** A commercial security firm successfully piloted 100 cameras deployed strategically across their main office. Camera images were uploaded to Amazon S3, tagged using Amazon Rekognition, and findings saved in Amazon ES. The firm now explores expanding into a complete production system with hundreds of video cameras across worldwide office sites for real-time non-employee activity detection. Which option should they consider?

**Options:**

- A) Use a proxy server at each local office and for each camera, stream the RTSP feed to a unique Amazon Kinesis Video Streams video stream. On each stream, use Amazon Rekognition Video and create a stream processor to detect faces from a collection of known employees, and alert when non-employees are detected.
- B) Use a proxy server at each local office and for each camera, stream the RTSP feed to a unique Amazon Kinesis Video Streams video stream. On each stream, use Amazon Rekognition Image to detect faces from a collection of known employees and alert when non-employees are detected.
- C) Install AWS DeepLens cameras and use the DeepLens_Kinesis_Video module to stream video to Amazon Kinesis Video Streams for each camera. On each stream, use Amazon Rekognition Video and create a stream processor to detect faces from a collection on each stream, and alert when non-employees are detected.
- D) Install AWS DeepLens cameras and use the DeepLens_Kinesis_Video module to stream video to Amazon Kinesis Video Streams for each camera. On each stream, run an AWS Lambda function to capture image fragments and then call Amazon Rekognition Image to detect faces from a collection of known employees, and alert when non-employees are detected.

**정답: A) 프록시 서버를 통한 RTSP 스트리밍 + Kinesis Video Streams + Rekognition Video 스트림 프로세서**

💡 추가 설명:

- **옵션 B** - Rekognition Image는 개별 이미지 처리 방식으로 실시간 비디오 스트림 처리에 부적합하며 연속성 있는 모니터링 불가능
- **옵션 C** - DeepLens는 특수 AI 카메라로 기존 보안 카메라 인프라를 모두 교체해야 하므로 비용이 과도하고 글로벌 확장 시 비현실적
- **옵션 D** - Lambda를 통한 이미지 캡처 방식은 불필요한 중간 처리 단계로 지연 시간이 증가하여 실시간 감지 요구사항에 부적합