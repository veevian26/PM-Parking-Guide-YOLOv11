SAFEKICK 🛴 — On-device AI PM Parking Guidance System
YOLO11 기반 온디바이스 AI 실시간 PM 주차 가이드 시스템
Konyang University, Department of Artificial Intelligence — AI Project I (2026)
________________________________________
📌 프로젝트 개요 (Project Overview)
Existing electric scooter return systems rely only on GPS-based parking zones — they cannot detect whether a scooter is parked near prohibited areas such as crosswalks, fire hydrants, bollards, manholes, or tactile paving blocks.
SAFEKICK solves this by analyzing the return photo taken by the user using two YOLO11-based models deployed directly on a mobile device (on-device AI):
•	A detection model to detect the scooter and prohibited objects (BBox)
•	A segmentation model to detect crosswalk regions (Polygon)
Result	Condition
✅ PASS — 반납 허용	Scooter is far enough from prohibited objects
❌ BLOCK — 반납 차단	Scooter is too close to a prohibited object
________________________________________
👥 팀 구성 (Team — Team 2)
이름	학번	역할
송지성 (팀장)	22619013	Project lead, model training, iOS app
허동회	22619022	Data preprocessing, experiment design
비비안 (Maduabum C.V.)	24619043	Model experiments, evaluation
________________________________________
🔄 워크플로우 (Workflow)
📷 사진 촬영  →  🤖 온디바이스 AI 추론  →  📐 주차 판단 로직  →  📱 결과 시각화
 Take photo       On-device inference       Parking logic          Show result
________________________________________
🎯 탐지 대상 클래스 (Detection Classes)
클래스	라벨링 방식	모델
전동킥보드 (Kickboard)	BBox	Detection
맨홀 (Manhole)	BBox	Detection
소화전 (Fire Hydrant)	BBox	Detection
볼라드 (Bollard)	BBox	Detection
점자블록 (Tactile Paving)	BBox	Detection
횡단보도 (Crosswalk)	Polygon	Segmentation
________________________________________
🤖 최종 선정 모델 (Selected Models)
역할	모델	Epoch
Object Detection (BBox)	YOLOv11s	70
Segmentation (Polygon)	YOLOv11n-seg	50
________________________________________
📐 주차 판단 로직 (Parking Decision Logic)
The system calculates a Normalized Distance R between the kickboard and each prohibited object:
R = d / L
•	d = shortest pixel distance between kickboard and the object
•	L = height of the kickboard bounding box (used for scale normalization)
판단	조건
✅ PASS (반납 허용)	R ≥ T
❌ BLOCK (반납 차단)	R < T
Threshold values (T) per object:
객체	임계값 (T)
맨홀 (Manhole)	0.7
점자블록 (Tactile Paving)	0.8
소화전 (Fire Hydrant)	1.1
횡단보도 (Crosswalk)	Segmentation polygon containment
________________________________________
📱 앱 화면 (App Screenshots)
서비스 앱 (Service App)
 
연구용 앱 (Research App)
 
________________________________________
🧪 결과 예시 (Result Examples)
<div align="center"> 
✅ 정상 반납 (Legal Parking)	❌ 위험 반납 (Illegal Parking)
 	 
킥보드 98% 신뢰도 탐지	점자블록과 너무 가까움 (R: 0.40 / T: 0.80)
금지 객체와의 거리 기준 이상	주차 불가 판정
</div> 
________________________________________
📊 온디바이스 성능 (On-device Performance)
기기	평균 추론시간	평균 메모리 사용량
iPhone 14 Pro	255 ms	391 MB
iPhone 17 Pro	200 ms	365 MB
________________________________________
🏕️ 필드테스트 결과 (Field Test Results)
회차	일시	장소	비고
1차	2026.06.03 (주간)	캠퍼스 주변	킥보드·맨홀·횡단보도 인식 우수
2차	2026.06.06 (주간)	관저동 일대	소화전·점자블록 임계값 조정
3차	2026.06.06 (야간)	관저동 일대	야간 조도 조건 성능 테스트
________________________________________
🗂️ 데이터세트 (Dataset)
출처	데이터명	크기
AI Hub
이륜자동차 안전 위험 시설물 데이터	7,000장 / 25.36GB
AI Hub
보행 안전을 위한 도로 시설물 데이터	-
________________________________________
⚙️ 개발 환경 (Environment)
•	Python 3.x
•	PyTorch (nightly, CUDA 12.8)
•	Ultralytics YOLOv11
•	iOS App (Flutter + Core ML)
•	GPU: NVIDIA RTX 5060 (Blackwell)
________________________________________
🚀 실행 방법 (How to Run)
# Install dependencies
pip install ultralytics

# Run detection model
yolo detect predict model=weights/detection_best.pt source=your_image.jpg

# Run segmentation model
yolo segment predict model=weights/segmentation_best.pt source=your_image.jpg
________________________________________
⚠️ 한계 및 향후 연구 (Limitations & Future Work)
•	야간 및 저조도 환경에서 인식 성능 저하
•	다양한 촬영 각도·거리·날씨 조건의 데이터 부족
•	향후: 데이터셋 확장, 거리 임계값(T) 자동 최적화, 실제 공유 PM 서비스 연계
________________________________________
📚 참고 자료 (References)-
•	Ultralytics YOLOv11
•	Apple Core ML
•	Flutter
•	AI Hub Dataset

