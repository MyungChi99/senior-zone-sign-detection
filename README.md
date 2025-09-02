# 🚗 라즈베리파이 기반 노인보호구역 표지판 실시간 탐지 시스템  

[![Python](https://img.shields.io/badge/Python-3.8+-blue?logo=python)](https://www.python.org/)  
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange?logo=tensorflow)](https://www.tensorflow.org/)  
[![Raspberry Pi](https://img.shields.io/badge/Raspberry%20Pi-4/5-red?logo=raspberrypi)](https://www.raspberrypi.com/)  
[![Docker](https://img.shields.io/badge/Docker-Supported-2496ED?logo=docker)](https://www.docker.com/)  
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)  

본 프로젝트는 **라즈베리파이 기반 임베디드 환경에서의 실시간 객체 탐지 시스템**입니다.  
딥러닝 모델 **YOLOv4**를 활용하여 **노인보호구역 4종 표지판**을 탐지하며,  
**PC 학습 → 라즈베리파이 배포** 과정에서 발생하는 최적화 문제 해결에 중점을 두었습니다.  

---

## 📌 프로젝트 아키텍처  

- 🖥️ **Server (RTX 3070 Ti Desktop)**  
  - 데이터 전처리, 모델 학습, 성능 평가  
  - TensorFlow Lite 변환  

- 🚗 **Edge Device (Raspberry Pi)**  
  - YOLOv4-tiny TFLite 모델을 이용한 실시간 추론  
  - Docker 기반 배포 환경 지원  

---

## ⚙️ 주요 기능  

✔️ 노인보호구역 관련 4종 표지판 실시간 탐지  
- `start_sign` : 보호구역 시작  
- `end_sign` : 보호구역 종료  
- `speed_sign` : 속도 제한  
- `ground_sign` : 노면 표시  

✔️ Docker 기반 라즈베리파이 배포  
✔️ Roboflow 기반 데이터셋 관리 및 증강  

---

## 📊 모델 성능  

| Metric     | Score   |
|------------|---------|
| mAP        | 74.3%   |
| Precision  | 79.7%   |
| Recall     | 75.1%   |

---

## 🎯 탐지 예시  

<!-- 실제 이미지 링크로 교체 -->
<img src="YOUR_IMAGE_LINK_HERE" width="400">  
<img src="YOUR_IMAGE_LINK_HERE" width="400">  

---

## 🚀 설치 및 실행 방법  

### 🔧 사전 요구사항  
- 학습 환경: `Python 3.8+`, `TensorFlow 2.x`, `CUDA`, `cuDNN`  
- 추론 환경: `Raspberry Pi 4/5`, `Raspberry Pi OS`, `Docker`  

### 1) 서버(학습)  
```bash
git clone https://github.com/YOUR_USERNAME/senior-protection-zone-detection.git
cd senior-protection-zone-detection/server_training
pip install -r requirements_train.txt
python train.py --data your_dataset.yaml --cfg yolov4.cfg --weights yolov4.weights --batch-size 16
```

### 2) 라즈베리파이(추론)  
```bash
git clone https://github.com/YOUR_USERNAME/senior-protection-zone-detection.git
cd senior-protection-zone-detection/deployment_raspberry_pi
# Dockerfile 기반 빌드 가능
python detect_realtime.py --model models/yolov4-tiny.tflite
```

---

## 📚 프로젝트를 통해 얻은 점  

- GPU ↔ 라즈베리파이 환경 차이를 통해 **모델 최적화의 필요성** 체감  
- 정확도 ↔ 속도 간 **트레이드오프 관계** 학습  
- Roboflow & Docker 활용을 통한 **데이터 관리/환경 통일** 경험  

---

## 🔮 한계점 및 개선 방향  

- **FPS 개선 필요**: Google Coral USB Accelerator 등 AI 가속기 추가 예정  
- **데이터셋 확장 필요**: 다양한 날씨/지역 데이터 확보 예정  

---

## 📄 라이선스  
이 프로젝트는 [MIT License](./LICENSE) 를 따릅니다.  
