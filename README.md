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

프로젝트 기간 내 도출된 **7 FPS**라는 결과를 바탕으로, 보고서에서 제시한 향후 과제를 직접 구현하였습니다.  
아래 4단계의 최적화를 순차적으로 적용하였습니다.

---

### 1단계: YOLOv4-Tiny로 모델 교체 (7 FPS → 17 FPS)

YOLOv4 Full 모델(Darknet-53, 53레이어)은 RPi 환경에서 연산량이 과도하여 실시간성 확보가 불가능하였습니다.  
보고서에서 향후 과제로 제시한 **YOLOv4-Tiny**로 교체하여 추론 속도를 개선하였습니다.
```python
# 기존 코드 (Full 모델)
net = cv2.dnn.readNet('yolov4.weights', 'yolov4.cfg')

# 교체 후 (Tiny 모델)
net = cv2.dnn.readNet('yolov4-tiny.weights', 'yolov4-tiny.cfg')
layer_names = net.getLayerNames()
output_layers = [layer_names[i - 1] for i in net.getUnconnectedOutLayers()]
```

**결과:** 약 7 FPS → 15~18 FPS

**발생한 문제:** Tiny 모델은 FPN(Feature Pyramid Network)이 단순화되어 있어, `ground_sign`(노면 표지)과 같이 작은 객체의 탐지율이 저하되었습니다. (mAP 74.3% → 약 61%)  
**해결:** 해당 클래스의 confidence threshold를 0.35로 낮춰 재현율을 보완하였습니다.

---

### 2단계: 입력 해상도 축소 416 → 320 (17 FPS → 23 FPS)

추론 입력 크기를 줄여 연산량을 추가로 감소시켰습니다.
```python
# 기존 전처리
blob = cv2.dnn.blobFromImage(
    image, scalefactor=1/255.0,
    size=(416, 416),  # 기존
    swapRB=True, crop=False
)

# 개선 후
blob = cv2.dnn.blobFromImage(
    image, scalefactor=1/255.0,
    size=(320, 320),  # 축소
    swapRB=True, crop=False
)
```

**결과:** 15~18 FPS → 22~25 FPS

**발생한 문제:** 다운샘플링으로 인해 2m 이상 거리의 표지판 인식률이 저하되었습니다.  
**해결:** 성능 한계를 숨기는 대신, **"유효 인식 거리 1.5m 이내"** 라는 동작 조건을 스펙으로 명확히 정의하였습니다.  
이를 통해 *"엔지니어링에서 성능 한계는 숨기는 것이 아니라 동작 조건으로 명확히 정의하는 것"* 임을 배웠습니다.

---

### 3단계: 멀티스레딩 — 캡처 / 추론 분리 (23 FPS → 29 FPS)

기존 코드는 캡처 → 추론 → 시각화가 순차적으로 실행되어, 추론 대기 시간 동안 캡처가 중단되는 구조였습니다.  
이를 스레드로 분리하여 병렬 처리하도록 재설계하였습니다.
```python
import threading
import queue

frame_queue = queue.Queue(maxsize=2)
result_queue = queue.Queue(maxsize=2)

def capture_worker():
    """RPi V2 카메라 전담 캡처 스레드"""
    cap = cv2.VideoCapture(0)
    cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
    cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)
    while True:
        ret, frame = cap.read()
        if not ret:
            continue
        # 큐가 가득 차면 오래된 프레임을 버려 최신성 유지
        if frame_queue.full():
            try:
                frame_queue.get_nowait()
            except queue.Empty:
                pass
        frame_queue.put(frame)

def inference_worker():
    """추론 전담 스레드"""
    while True:
        try:
            frame = frame_queue.get(timeout=1)
        except queue.Empty:
            continue
        blob = cv2.dnn.blobFromImage(
            frame, 1/255.0, (320, 320), swapRB=True, crop=False
        )
        net.setInput(blob)
        outputs = net.forward(output_layers)
        detections = parse_detections(outputs, frame.shape)
        result_queue.put((frame, detections))

t_capture = threading.Thread(target=capture_worker, daemon=True)
t_infer   = threading.Thread(target=inference_worker, daemon=True)
t_capture.start()
t_infer.start()

# 메인 루프: 시각화만 담당
while True:
    if not result_queue.empty():
        frame, detections = result_queue.get()
        visualize(frame, detections)
    cv2.waitKey(1)
```

**결과:** 22~25 FPS → 28~30 FPS

**발생한 문제:** `frame_queue.get_nowait()` 호출 시 두 스레드가 동시에 큐에 접근하며 `queue.Empty` 예외가 간헐적으로 발생하였습니다.  
**해결:** `try-except` 예외 처리로 해결하였으며, 이 과정에서 운영체제 수업에서 학습한 **임계구역(Critical Section) 문제**가 실제 시스템에서 동일하게 나타남을 확인하였습니다.  
Python의 `Queue`가 내부적으로 mutex를 사용하더라도, `get_nowait`과 같은 non-blocking 호출에서는 반드시 예외 처리가 필요합니다.

---

### 4단계: GPIO 연동 — 표지판 감지 시 하드웨어 경고 출력

보고서에서 목표로 제시한 **"차량 경고 시스템 연계"** 를 GPIO를 통해 직접 구현하였습니다.

**회로 구성:**
```
RPi GPIO 17핀 → 330Ω 저항 → LED(적색) → GND
RPi GPIO 27핀 → Active Buzzer → GND
```
```python
import RPi.GPIO as GPIO

LED_PIN    = 17
BUZZER_PIN = 27

GPIO.setmode(GPIO.BCM)
GPIO.setup(LED_PIN,    GPIO.OUT)
GPIO.setup(BUZZER_PIN, GPIO.OUT)

# 클래스별 경고 레벨 정의
WARNING_CLASSES = {
    'start_sign':  'HIGH',  # 보호구역 진입 → 강한 경고
    'speed_sign':  'HIGH',  # 속도 제한    → 강한 경고
    'end_sign':    'LOW',   # 보호구역 종료 → 약한 경고
    'ground_sign': 'LOW',   # 노면 표지    → 약한 경고
}

hw_queue = queue.Queue()

def hw_control_worker():
    """GPIO 제어 전담 스레드"""
    while True:
        try:
            level = hw_queue.get(timeout=1)
        except queue.Empty:
            GPIO.output(LED_PIN,    GPIO.LOW)
            GPIO.output(BUZZER_PIN, GPIO.LOW)
            continue

        if level == 'HIGH':
            for _ in range(3):  # 빠른 3회 점멸
                GPIO.output(LED_PIN,    GPIO.HIGH)
                GPIO.output(BUZZER_PIN, GPIO.HIGH)
                time.sleep(0.1)
                GPIO.output(LED_PIN,    GPIO.LOW)
                GPIO.output(BUZZER_PIN, GPIO.LOW)
                time.sleep(0.1)
        elif level == 'LOW':
            GPIO.output(LED_PIN, GPIO.HIGH)
            time.sleep(0.3)
            GPIO.output(LED_PIN, GPIO.LOW)

t_hw = threading.Thread(target=hw_control_worker, daemon=True)
t_hw.start()
```

**발생한 문제:** GPIO 제어 코드를 추론 스레드 내부에 배치하였을 때, `time.sleep()` 이 추론 루프를 블로킹하여 FPS가 29에서 12로 급감하였습니다.  
**해결:** GPIO 제어를 독립된 스레드로 분리하고 큐를 통해 이벤트를 전달하는 방식으로 해결하였습니다.  
각 스레드가 **단일 책임(Single Responsibility)** 을 갖도록 설계하는 것이 실시간 시스템에서 필수적임을 배웠습니다.

---

### 📈 최적화 단계별 성능 요약

| 단계 | 적용 기법 | FPS | 핵심 배움 |
|------|-----------|-----|-----------|
| 0 | 초기 상태 (YOLOv4 Full, 416) | ~7 | - |
| 1 | YOLOv4-Tiny 모델 교체 | ~17 | 경량화 vs 정확도 트레이드오프 |
| 2 | 입력 해상도 416→320 축소 | ~23 | 동작 조건의 명확한 정의 |
| 3 | 멀티스레딩 구조 적용 | ~29 | 임계구역, 최신성 우선 설계 |
| 4 | GPIO 경고 스레드 분리 | ~29 + HW | 단일 책임 원칙 |
---
## 개선점을 반영한 새로운 프로젝트 기획
- 2025년 임베디드 공모전을 위한 프로젝트 계획서를 해당 프로젝트에서 배운 한계점들을 바탕으로 재설계 하였습니다.
- [2025ESWContest_모빌리티_6120_MUX4_개발계획서.docx](https://github.com/user-attachments/files/22085052/2025ESWContest_._6120_MUX4_.docx)



---
## 📄 라이선스  
이 프로젝트는 [MIT License](./LICENSE) 를 따릅니다.  
