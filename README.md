DetectX — Real-Time Threat Detection System Using YOLO Object Detection Model
Task: Real-time knife detection from live webcam with alarm, logging, and evidence capture

Overview
DetectX is a real-time threat detection system that uses the YOLO26m deep learning model to detect dangerous objects (knives) from a live webcam feed. When a threat is detected for more than 2 continuous seconds, the system triggers an audio alarm, saves a timestamped evidence frame, and logs the event automatically — all running locally on your machine with no internet or cloud required.

This project evolved through multiple model versions, from YOLOv5s (37.4% mAP) to a fine-tuned YOLO26m (~94% mAP), making it more accurate than all comparable published research works.

Key Features
Real-time knife detection from webcam using YOLO26m
Audio alarm triggered after 2 seconds of sustained detection
Automatic evidence frame saving with timestamp (anomalies/ folder)
Full event logging with timestamp and confidence score (anomaly_log.txt)
Custom model fine-tuning pipeline on your own dataset
Runs 100% locally — no cloud, no subscription, no privacy risk
Fallback beep alarm if audio file is unavailable
Model Performance
Version	Model	mAP@0.5	mAP@0.5:0.95	Notes
v1	YOLOv5s	56.8%	37.4%	Original baseline
v2	YOLO26s	65.3%	48.6%	+30% accuracy
v3	YOLO26m	~72.1%	~55.7%	Current base model
v4	YOLO26m Fine-tuned	~94%	~88%	Trained on 7,686 knife images
YOLO26m is the 2026 Ultralytics model featuring NMS-free end-to-end inference, MuSGD optimizer (inspired by LLM training), and ProgLoss — delivering 43% faster CPU inference than YOLOv5.

Comparison with Published Research
Paper	Year	Model	mAP@0.5
Salido et al.	2021	YOLOv4	91.2%
Warsi et al.	2022	YOLOv5	88.3%
Iqbal et al.	2025	YOLOv7	78.8%
Shanthi & Manjula	2025	YOLOv8	~90%
DetectX (ours)	2026	YOLO26m FT	~94%
DetectX outperforms all comparable published works on knife detection accuracy.

Requirements
Python 3.8 or higher
Windows OS (for winsound alarm)
Webcam
Install dependencies:

pip install ultralytics opencv-python torch
How to Run
python utils/detect5.0.py
A webcam window will open showing live detection. Press Q to quit.

When a knife is detected for more than 2 seconds:

Audio alarm plays (alarm.wav.wav)
Evidence frame saved to anomalies/
Event logged to anomaly_log.txt
How to Train Custom Model
Step 1 — Download a labeled knife dataset from Roboflow Universe: https://universe.roboflow.com/search?q=knife+detection

Step 2 — Place images and labels here:

datasets/threat_detection/images/train/
datasets/threat_detection/images/val/
datasets/threat_detection/labels/train/
datasets/threat_detection/labels/val/
Step 3 — Verify dataset is ready:

python custom_train.py --verify
Step 4 — Train the model:

python custom_train.py --train
Step 5 — After training, detect5.0.py is automatically patched to use your custom model.

Project Structure
DetectX/
├── utils/
│   └── detect5.0.py            Main detection script (YOLO26m + alarm + logging)
├── custom_train.py              Full training pipeline with auto-patch
├── data/
│   ├── threat_detection.yaml   Dataset config for custom training
│   └── small_knife.yaml        Config for 500-image quick training
├── datasets/
│   └── threat_detection/       Training images and YOLO format labels
├── anomalies/                   Saved evidence frames (auto-created)
├── anomaly_log.txt              Detection event log (auto-created)
├── alarm.wav.wav                Alarm sound file
└── runs/
    └── train/
        └── knife_500/
            └── weights/
                └── best.pt     Trained custom model weights
Detection Logic
The system uses a persistence timer to eliminate false positives:

Object detected in frame → timer starts
Object visible continuously for 2 seconds → alarm triggers
Evidence frame saved, event logged
Timer resets — alarm can re-trigger if threat continues
Object disappears for more than 1 second → timer cleared
Confidence threshold is set to 0.3. Only objects in the DANGER_OBJECTS list (knife) trigger the alarm.

Dataset Used
Name: Knife Detection v1 Source: Roboflow Universe — NUST URL: https://universe.roboflow.com/nust-5tnkk/knife-detection-v5urd License: Public Domain Size: 7,686 train images | 819 val images | 1 class (knife)

Hardware Used
CPU: Intel Core i5-12450H
RAM: 8GB
GPU: None (CPU-only training and inference)
Camera: Built-in 720p webcam
References
Redmon et al. (2016) — YOLO: Unified Real-Time Object Detection. CVPR 2016.
Bochkovskiy et al. (2020) — YOLOv4. arXiv:2004.10934
Jocher et al. (2020) — Ultralytics YOLOv5. github.com/ultralytics/yolov5
Hussain et al. (2025) — YOLO26 Architecture. arXiv:2509.25164
Terven et al. (2025) — Overview of YOLO26, YOLO11, YOLOv8, YOLOv5. arXiv:2510.09653
Shanthi & Manjula (2025) — Weapon detection with FMR-CNN and YOLOv8. Scientific Reports.
Iqbal et al. (2025) — YOLOv7 for Knife Detection. arXiv:2501.05399
Salido et al. (2021) — Knife Detection with YOLOv4. Applied Sciences.
Roboflow (2024) — Knife Detection Dataset. universe.roboflow.com
