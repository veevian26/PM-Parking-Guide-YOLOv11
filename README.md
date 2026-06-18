# SAFEKICK  — On-device AI PM Parking Guidance System

**YOLO11 기반 온디바이스 AI 실시간 PM 주차 가이드 시스템**

> *Konyang University, Department of Artificial Intelligence — AI Project I*

---

## 프로젝트 개요 (Project Overview)

Existing electric scooter return systems rely only on GPS-based parking zones — they **cannot detect** whether a scooter is parked near prohibited areas such as crosswalks, fire hydrants, bollards, manholes, or tactile paving blocks.

**SAFEKICK** solves this by analyzing the return photo taken by the user using **two YOLO11-based models** deployed directly on a mobile device (on-device AI):

- A **detection model** to detect the scooter and prohibited objects (BBox)
- A **segmentation model** to detect crosswalk regions (Polygon)

The AI inference runs **entirely on-device** — no server, no internet required — enabling real-time parking validation during the return process.

### Key Features
-  **Photo-based validation** — user takes a photo at return time
-  **Dual model system** — object detection + segmentation working together
-  **Normalized distance logic** — scale-invariant parking decision engine
-  **iOS on-device inference** — using Core ML, no server dependency
-  **Auto-block** — prevents return if parking is unsafe, guides user to re-park

### 기존 시스템과의 차별점 (vs Existing Systems)

| | GPS 기반 시스템 | **SAFEKICK** |
|--|--|--|
| 위치 확인 | ✅ | ✅ |
| 실제 주차 환경 분석 | ❌ | ✅ |
| 점자블록·횡단보도 인식 | ❌ | ✅ |
| 서버 없이 작동 | ❌ | ✅ |
| 실시간 처리 | ❌ | ✅ |



---

##  워크플로우 (Workflow)

<img src="https://raw.githubusercontent.com/veevian26/PM-Parking-Guide-YOLOv11/main/flowchart.jpeg" width="700"/>


---
##  팀 구성 (Team)

| 이름 | 역할 |
|------|------|
| 송지성 | Project lead, model training, iOS app |
| 허동회 | Data preprocessing, experiment design |
| 비비안 | Model experiments, evaluation |


---

##  탐지 대상 클래스 (Detection Classes)

| 클래스 | 라벨링 방식 | 모델 |
|--------|------------|------|
| 전동킥보드 (Kickboard) | BBox | Detection |
| 맨홀 (Manhole) | BBox | Detection |
| 소화전 (Fire Hydrant) | BBox | Detection |
| 볼라드 (Bollard) | BBox | Detection |
| 점자블록 (Tactile Paving) | BBox | Detection |
| 횡단보도 (Crosswalk) | Polygon | Segmentation |

---

##  최종 선정 모델 (Selected Models)

| 역할 | 모델 | Epoch |
|------|------|-------|
| Object Detection (BBox) | **YOLO11s** | 70 |
| Segmentation (Polygon) | **YOLO11n-seg** | 50 |

---

##  주차 판단 로직 (Parking Decision Logic)

The system calculates a **Normalized Distance R** between the kickboard and each prohibited object:

```
R = d / L
```

- `d` = shortest pixel distance between kickboard and the object  
- `L` = height of the kickboard bounding box (used for scale normalization)

| 판단 | 조건 |
|------|------|
| ✅ PASS (반납 허용) | R ≥ T |
| ❌ BLOCK (반납 차단) | R < T |

**Threshold values (T) per object:**

| 객체 | 임계값 (T) |
|------|-----------|
| 맨홀 (Manhole) | 0.7 |
| 점자블록 (Tactile Paving) | 0.8 |
| 소화전 (Fire Hydrant) | 1.1 |
| 횡단보도 (Crosswalk) | Segmentation polygon containment |

---

##  앱 화면 

### 서비스 앱 (Service App)

<img src="https://raw.githubusercontent.com/veevian26/PM-Parking-Guide-YOLOv11/main/service_app.jpeg" width="700"/>

### 연구용 앱 (Research App)

<img src="https://raw.githubusercontent.com/veevian26/PM-Parking-Guide-YOLOv11/main/reaearch_app.jpeg" width="700"/>

---

## 결과 예시 (Result Examples)

<div align="center">
  <table>
    <tr>
      <th>✅ 정상 반납 (Legal Parking)</th>
      <th>❌ 위험 반납 (Illegal Parking)</th>
    </tr>
    <tr>
      <td><img src="https://raw.githubusercontent.com/veevian26/PM-Parking-Guide-YOLOv11/main/legal_parking.jpeg" width="300"/></td>
      <td><img src="https://raw.githubusercontent.com/veevian26/PM-Parking-Guide-YOLOv11/main/illagal_parking.jpeg" width="300"/></td>
    </tr>
    <tr>
      <td align="center">킥보드 98% 신뢰도 탐지</td>
      <td align="center">점자블록과 너무 가까움 (R: 0.40 / T: 0.80)</td>
    </tr>
  </table>
</div>

---

##  온디바이스 성능 (On-device Performance)

| 기기 | 평균 추론시간 | 평균 메모리 사용량 |
|------|------------|----------------|
| iPhone 14 Pro | **255 ms** | 391 MB |
| iPhone 17 Pro | **200 ms** | 365 MB |

---

##  필드테스트 결과 (Field Test Results)

| 회차 | 일시 | 장소 | 비고 |
|------|------|------|------|
| 1차 | 2026.06.03 (주간) | 캠퍼스 주변 | 킥보드·맨홀·횡단보도 인식 우수 |
| 2차 | 2026.06.06 (주간) | 관저동 일대 | 소화전·점자블록 임계값 조정 |
| 3차 | 2026.06.06 (야간) | 관저동 일대 | 야간 조도 조건 성능 테스트 |

---

## 데이터세트 (Dataset)

| 출처 | 데이터명 | 크기 |
|------|---------|------|
| [AI Hub](https://aihub.or.kr) | 이륜자동차 안전 위험 시설물 데이터 | 7,000장 / 25.36GB |
| [AI Hub](https://aihub.or.kr) | 보행 안전을 위한 도로 시설물 데이터 | - |

---

##  개발 환경 (Environment)

- Python 3.x
- PyTorch (nightly, CUDA 12.8)
- Ultralytics YOLOv11
- iOS App (Flutter + Core ML)
- GPU: NVIDIA RTX 5060 (Blackwell)

---

##  실행 방법 (How to Run)

```bash
# Install dependencies
pip install ultralytics

# Run detection model
yolo detect predict model=weights/detection_best.pt source=your_image.jpg

# Run segmentation model
yolo segment predict model=weights/segmentation_best.pt source=your_image.jpg
```

---

## 한계 및 향후 연구 (Limitations & Future Work)

- 야간 및 저조도 환경에서 인식 성능 저하
- 다양한 촬영 각도·거리·날씨 조건의 데이터 부족
- **향후:** 데이터셋 확장, 거리 임계값(T) 자동 최적화, 실제 공유 PM 서비스 연계

---

## 참고 자료 (References)

- [Ultralytics YOLOv11](https://docs.ultralytics.com)
- [Apple Core ML](https://developer.apple.com/documentation/coreml)
- [Flutter](https://docs.flutter.dev)
- [AI Hub Dataset](https://aihub.or.kr)
