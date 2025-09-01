🚗 라즈베리파이 기반 노인보호구역 표지판 실시간 탐지 시스템
1. 프로젝트 소개
본 프로젝트는 임베디드 환경(라즈베리파이)에서의 실시간 객체 탐지를 목표로, 딥러닝 모델(YOLOv4)을 사용하여 노인보호구역 관련 4종 표지판을 탐지하는 시스템을 개발했습니다. PC 환경에서 모델을 학습시키고, 실제 차량과 유사한 저전력/저사양 환경에 배포하는 과정에서 발생하는 최적화 문제 해결에 중점을 두었습니다.

2. 시스템 아키텍처
본 프로젝트는 **서버(학습)**와 **엣지 디바이스(추론)**의 역할을 명확히 분리한 MLOps 파이프라인을 따릅니다.

🖥️ Server (RTX 3070 Ti Desktop): 데이터 전처리, 모델 학습, 성능 평가 및 TFLite 모델 변환 등 모든 무거운 연산을 담당합니다.

🚗 Edge Device (Raspberry Pi): 서버로부터 전달받은 경량화 모델을 이용해, 실제 환경에서 실시간 추론(Inference)만 수행합니다.

3. 주요 기능 및 결과
주요 기능
노인보호구역 관련 4종 표지판 실시간 탐지

start_sign: 보호구역 시작

end_sign: 보호구역 종료

speed_sign: 속도 제한

ground_sign: 노면 표시

Docker를 활용한 라즈베리파이 추론 환경 간소화

Roboflow 플랫폼을 이용한 체계적인 데이터 관리 및 증강

최종 모델 성능
Metric

Score

mAP

74.3%

Precision

79.7%

Recall

75.1%

탐지 예시
<!-- 실제 탐지 성공 이미지들을 여기에 추가하세요 -->

<img src="YOUR_IMAGE_LINK_HERE" width="400">
<img src="YOUR_IMAGE_LINK_HERE" width="400">

4. 설치 및 실행 방법
사전 요구사항
학습 환경: Python 3.8+, TensorFlow 2.x, CUDA, cuDNN

추론 환경: Raspberry Pi 4 or 5, Raspberry Pi OS, Docker

1) 서버(학습) 환경 설정
# 1. 리포지토리 클론
git clone [https://github.com/YOUR_USERNAME/senior-protection-zone-detection.git](https://github.com/YOUR_USERNAME/senior-protection-zone-detection.git)
cd senior-protection-zone-detection/server_training

# 2. 필요 패키지 설치
pip install -r requirements_train.txt

# 3. 모델 학습 실행 (커스텀 데이터셋 사용)
python train.py --data your_dataset.yaml --cfg yolov4.cfg --weights yolov4.weights --batch-size 16

2) 라즈베리파이(추론) 환경 설정
# 1. 리포지토리 클론
git clone [https://github.com/YOUR_USERNAME/senior-protection-zone-detection.git](https://github.com/YOUR_USERNAME/senior-protection-zone-detection.git)
cd senior-protection-zone-detection/deployment_raspberry_pi

# 2. (선택) Docker 환경 실행
# Dockerfile을 사용하여 추론 환경을 빌드하고 실행합니다.

# 3. 실시간 탐지 실행
python detect_realtime.py --model models/yolov4-tiny.tflite

5. 프로젝트를 통해 얻은 점 (Key Learnings)
데스크탑과 임베디드 환경의 간극: 고성능 GPU 환경에서 잘 동작하던 모델도, CPU 기반의 라즈베리파이에서는 속도 저하가 심각하다는 것을 체감했습니다. 이는 모델 경량화와 최적화가 연구 단계를 넘어 실제 제품화에 얼마나 필수적인지 깨닫는 계기가 되었습니다.

'최적화'의 실제 의미: TensorFlow Lite 변환, 모델 구조 변경(e.g., YOLOv4-tiny) 등의 최적화 기법이 정확도(Accuracy)와 속도(Latency) 사이의 트레이드오프 관계에 있음을 확인했습니다. 제한된 자원 내에서 목표 성능을 달성하기 위한 엔지니어링적 고민의 중요성을 배웠습니다.

체계적인 개발 파이프라인의 중요성: Roboflow를 통한 데이터 증강, Docker를 통한 배포 환경 통일 등 자동화된 도구의 활용이 전체 개발 시간을 단축하고, 반복적인 실험에서 발생하는 오류를 크게 줄여준다는 것을 경험했습니다.

6. 한계점 및 향후 개선 방향
FPS(Frames Per Second) 한계: 현재 CPU 기반 추론으로는 실시간성을 완전히 확보하기에 FPS가 부족합니다. 향후 Google Coral USB Accelerator와 같은 AI 가속기를 추가하여 NPU 연산을 통해 FPS를 30 이상으로 끌어올릴 계획입니다.

데이터셋 확장: 서울 일부 지역의 데이터만 사용해 특정 환경에 과적합(Overfitting)될 수 있습니다. 다양한 날씨(비, 눈, 야간)와 지역의 데이터를 추가 수집하여 모델의 강건함(Robustness)을 높일 필요가 있습니다
