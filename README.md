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

<img width="400" alt="Image" src="https://github.com/user-attachments/assets/9ada21f1-bee8-40b9-9bcf-f34d508d75c8" />
<img width="300" alt="Image" src="https://github.com/user-attachments/assets/203fa357-c2b6-48fe-9020-93c57b7b28e7" />

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



---

## 🔮 한계점 및 개선 방향  

- **FPS 개선 필요**: 라즈베리파이 모듈들의 성능을 테스트해보지 않고 단순히 카메라 모듈이면 모두 동일하게 사용 가능할거라는 착각으로 fps는 최악의 결과를 가져옴 (실제 fps 5~7)
  그결과 실사용에서는 불가능한 아쉬운 결과물이 생겼습니다. 대신 다음 프로젝트에서는 반드시 MCU의 모듈 부품들의 성능도 반드시 체크하여 프로젝트를 진행 혹은 사용 경험이 많은 전자,기계 공학과 학부와 함께 협업하여
  프로젝트를 구현할 계획입니다.
- **데이터셋 확장 필요**: 밝은 날에 서울에서 수집한 데이터셋 만을 사용해서 학습을 시켰기 때문에 야간 환경, 야간 환경에서의 어두움, 비오는 환경, 비가 올때 가려지는 시야등은 전혀 고려하지 않았습니다. 때문에 다음 프로젝트를
  구현할때는 여러 모듈을 사용하여 종합한 데이터를 학습하는 아키텍처를 설계할 것을 계획하였습니다.  

---
## 개선점을 반영한 새로운 프로젝트 기획
- 2025년 임베디드 공모전을 위한 프로젝트 계획서를 해당 프로젝트에서 배운 한계점들을 바탕으로 재설계 하였습니다.
- [2025ESWContest_모빌리티_6120_MUX4_개발계획서.docx](https://github.com/user-attachments/files/22085052/2025ESWContest_._6120_MUX4_.docx)



---
## 📄 라이선스  
이 프로젝트는 [MIT License](./LICENSE) 를 따릅니다.  
