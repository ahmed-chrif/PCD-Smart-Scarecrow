# 🌾 Smart Scarecrow

<p align="center">
  <img src="https://img.shields.io/badge/Smart%20Agriculture-🌱-4CAF50?style=for-the-badge" alt="Smart Agriculture">
  <img src="https://img.shields.io/badge/Computer%20Vision-MobileNetV2-blue?style=for-the-badge" alt="Computer Vision">
  <img src="https://img.shields.io/badge/Audio%20Detection-DS--CNN-orange?style=for-the-badge" alt="Audio Detection">
  <img src="https://img.shields.io/badge/Backend-Spring%20Boot-green?style=for-the-badge" alt="Spring Boot">
  <img src="https://img.shields.io/badge/Frontend-Angular-red?style=for-the-badge" alt="Angular">
</p>

<p align="center">
  <b>🌱 An intelligent agricultural surveillance and bird protection system</b><br>
  Combining 📷 image detection, 🎙️ sound detection, and 🚨 automatic deterrence.
</p>

---

## 🌟 Overview

**Smart Scarecrow** is an intelligent agricultural monitoring system designed to detect birds and unusual sounds around a farm.

The system combines two detection methods:

- 📷 **Camera-based detection** using an ESP32-CAM
- 🎙️ **Sound-based detection** using a Raspberry Pi 4 and an INMP441 microphone
- 🚨 **Siren control** for automatic or manual bird deterrence
- 📊 **Web dashboard** for monitoring detections and system activity

The project connects embedded devices, machine learning models, a backend server, a database, and a web interface into one system.

---

## 🏗️ System Architecture

```text
                         🌾 SMART SCARECROW
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
        📷 IMAGE DETECTION                  🎙️ SOUND DETECTION
              │                                   │
         ESP32-CAM                            Raspberry Pi 4
         OV2640 Camera                        INMP441 Microphone
              │                                   │
              └─────────────────┬─────────────────┘
                                │
                         🧠 Detection Logic
                                │
                         🚨 Siren Control
                                │
                         🌐 Backend API
                                │
                         🗄️ PostgreSQL
                                │
                         📊 Angular Dashboard
```

---

## 📷 Image Detection

The camera part uses an **ESP32-CAM** with an **OV2640** camera.

Captured images are processed using a **MobileNetV2** model.

### 🔄 Processing Pipeline

```text
📷 Camera
   ↓
🖼️ Image Capture
   ↓
🔄 RGB Conversion
   ↓
📐 Resize to 224 × 224
   ↓
⚖️ Normalize Pixel Values
   ↓
🧠 MobileNetV2
   ↓
📊 Prediction + Confidence
```

### ⚙️ Image Preprocessing

The image is:

1. Converted from BGR to RGB
2. Resized to `224 × 224`
3. Normalized using `/ 255.0`
4. Converted into a batch before inference

```python
image_rgb = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2RGB)
image_resized = cv2.resize(image_rgb, IMG_SIZE)
image_norm = image_resized / 255.0
image_batch = np.expand_dims(image_norm, axis=0)
```

### 🎯 Detection Classes

```python
CLASSES = ["BIRD", "SOUND"]
SEUIL = 0.70
```

The predicted class is selected using the highest model probability.

```python
predictions = model.predict(img, verbose=0)
idx = np.argmax(predictions[0])
confidence = predictions[0][idx]
```

---

## 🎙️ Sound Detection

The sound detection system runs on the **Raspberry Pi 4**.

It uses:

- 🎙️ **INMP441 I²S microphone**
- 🧠 **DS-CNN with attention**
- 🔊 Audio processing with **librosa**
- ⚡ TensorFlow Lite for inference

### 🔄 Audio Pipeline

```text
🎙️ INMP441
    ↓
🎵 Audio Capture
    ↓
📊 Audio Preprocessing
    ↓
🎚️ Mel Spectrogram
    ↓
🧠 DS-CNN + Attention
    ↓
📈 Confidence
    ↓
🚨 Detection Event
```

---

## 🔗 Detection Combination

The system supports three detection methods:

```typescript
export type DetectionMethod = 'CAMERA' | 'SOUND' | 'BOTH';
```

| Method | Source | Description |
|---|---|---|
| 📷 `CAMERA` | ESP32-CAM | Detection based on image |
| 🎙️ `SOUND` | Raspberry Pi 4 | Detection based on audio |
| 🔗 `BOTH` | Camera + Sound | Both detection sources report an event |

This allows the system to keep the detection history consistent when both sources detect an event.

---

## 🚨 Siren / Deterrent System

The system includes a siren that can be controlled in different modes.

```typescript
export type SirenMode = 'AUTO' | 'MANUAL' | 'DISABLED';
```

| Mode | Behavior |
|---|---|
| 🤖 `AUTO` | Siren can be triggered automatically |
| 🖐️ `MANUAL` | Siren is controlled manually |
| 🔕 `DISABLED` | Siren remains disabled |

The system records whether the siren was triggered for each detection.

---

## 🍓 Raspberry Pi 4

The Raspberry Pi acts as the main processing device for the sound detection part.

### Main components

- 🧠 TensorFlow Lite inference
- 🎙️ INMP441 microphone
- 🎵 Audio processing
- 📷 OpenCV
- 🌐 Python server
- 🔌 Communication bridge

### Main scripts

```text
rpi4/
├── bridge.py
├── server_complete.py
├── server.py
├── opencv_bird_sound.py
└── test_*.py
```

---

## 📷 ESP32-CAM

The ESP32-CAM provides the camera input.

### Hardware

- 🔲 ESP32-CAM
- 📷 OV2640 camera
- 🌐 Network communication
- 🚨 Connection to the detection system

Firmware:

```text
farmwatch_esp32cam.ino
```

---

## 🌐 Backend

The backend is implemented using **Spring Boot 3**.

### Main technologies

- ☕ Java
- 🍃 Spring Boot 3
- 🔐 Spring Security
- 🔑 JWT authentication
- 🗄️ Spring Data JPA
- 🔌 REST API
- ⚡ WebSocket / STOMP
- 🐘 PostgreSQL

### Backend structure

```text
backend/
└── src/main/java/com/farmwatch/
    ├── controller/
    ├── service/
    ├── entity/
    ├── repository/
    ├── dto/
    ├── config/
    └── websocket/
```

The backend is responsible for:

- 🔐 User authentication
- 📡 REST API
- 📊 Detection management
- 🚨 Siren management
- 🔄 Real-time WebSocket updates
- 🗄️ Database communication

---

## 🗄️ Database

The project uses **PostgreSQL**.

Database schema:

```text
database/
└── schema.sql
```

The database stores information related to detections, including:

- 🕒 Detection date and time
- 📡 Detection method
- 📈 Confidence
- 🐦 Estimated species
- 📍 Sector
- 🚨 Siren status
- ⏱️ Duration
- 🖼️ Image path

---

## 📊 Detection Model

The frontend uses the following detection model:

```typescript
export interface Detection {
  id: string;
  detectedAt: string;
  method: DetectionMethod;
  confidence: number;
  speciesEst: string | null;
  sectorCode: string;
  sectorName: string;
  sirenTriggered: boolean;
  durationSecs: number | null;
  imagePath: string | null;
}
```

This structure is used to display detection information in the dashboard.

---

## 💻 Frontend

The frontend is built with **Angular 17**.

### Main technologies

- 🅰️ Angular 17
- 📘 TypeScript
- 🔌 STOMP.js
- 🌐 SockJS
- 📊 Dashboard components

### Structure

```text
frontend/
└── src/app/
    ├── components/
    ├── services/
    └── models/
```

The dashboard provides:

- 📺 Live monitoring
- 📊 Detection statistics
- 📈 Hourly charts
- 🗓️ Weekly heatmap
- 📋 Detection history
- 🔐 Authentication
- ⚡ Real-time updates

---

## 🧠 Machine Learning

### 📷 Image Model

**MobileNetV2** is used for image classification.

```text
Input Image
    ↓
224 × 224
    ↓
Normalization
    ↓
MobileNetV2
    ↓
Class Prediction
    ↓
Confidence
```

### 🎙️ Audio Model

The audio detection uses:

**DS-CNN + Attention**

with:

- 🎵 Mel spectrogram features
- 🐍 Python
- ⚡ TensorFlow Lite
- 🎧 librosa

---

## 📚 Dataset

The image dataset is divided into three parts:

| Dataset | Percentage |
|---|---:|
| 🏋️ Training | 70% |
| 🔎 Validation | 15% |
| 🧪 Testing | 15% |

The dataset script uses:

```python
TRAIN = 0.70
VALID = 0.15
TEST = 0.15
MAX_PAR_CLASSE = 4000
```

The random seed is:

```python
random.seed(42)
```

Classes:

```text
dataset/
├── bird/
└── sound/
```

---

## 🧰 Technology Stack

| Layer | Technology |
|---|---|
| 🌐 Frontend | Angular 17 |
| 📘 Frontend Language | TypeScript |
| ⚡ Real-time | STOMP.js / SockJS |
| ☕ Backend | Spring Boot 3 |
| 🔐 Security | Spring Security + JWT |
| 🗄️ Database | PostgreSQL |
| 📡 API | REST |
| 🔄 Communication | WebSocket |
| 📷 Camera | ESP32-CAM + OV2640 |
| 🎙️ Microphone | INMP441 |
| 🧠 Vision | MobileNetV2 |
| 🎵 Audio | DS-CNN + Attention |
| ⚡ ML Runtime | TensorFlow Lite |
| 🐍 Edge Software | Python |
| 🖼️ Vision Processing | OpenCV |
| 🎧 Audio Processing | librosa |
| 🔊 Deterrence | Buzzer / Siren |

---

## 📁 Project Structure

```text
PCD/
│
├── farmwatch/
│   ├── backend/
│   │   ├── src/main/java/com/farmwatch/
│   │   │   ├── controller/
│   │   │   ├── service/
│   │   │   ├── entity/
│   │   │   ├── repository/
│   │   │   ├── dto/
│   │   │   ├── config/
│   │   │   └── websocket/
│   │   ├── src/main/resources/
│   │   │   └── application.yml
│   │   └── pom.xml
│   │
│   ├── frontend/
│   │   └── src/app/
│   │       ├── components/
│   │       ├── services/
│   │       └── models/
│   │
│   ├── database/
│   │   └── schema.sql
│   │
│   └── farmwatch_esp32cam.ino
│
└── rpi4/
    ├── bridge.py
    ├── server_complete.py
    ├── server.py
    ├── opencv_bird_sound.py
    └── test_*.py
```

---

## 🚀 Running the Project

### ☕ Backend

```bash
cd farmwatch/backend
mvn clean install
mvn spring-boot:run
```

### 🅰️ Frontend

```bash
cd farmwatch/frontend
npm install
npm start
```

### 🐍 Raspberry Pi

```bash
cd rpi4
python3 server_complete.py
```

Communication bridge:

```bash
python3 bridge.py
```

### 📷 ESP32-CAM

Open:

```text
farmwatch_esp32cam.ino
```

Then configure the board and upload the firmware using the Arduino environment.

---

## 🔄 System Workflow

```text
        🌾 Farm Environment
                │
        ┌───────┴───────┐
        │               │
       📷              🎙️
    ESP32-CAM       Raspberry Pi
        │               │
        ▼               ▼
   🧠 Vision        🧠 Audio
    Model             Model
        │               │
        └───────┬───────┘
                ▼
          📊 Detection
                │
                ▼
           🚨 Siren Logic
                │
                ▼
          🌐 Spring Boot
                │
          ┌─────┴─────┐
          ▼           ▼
      🗄️ Database   ⚡ WebSocket
                        │
                        ▼
                  📊 Dashboard
```

---

## 📌 Important Code References

### Detection methods

```typescript
export type DetectionMethod = 'CAMERA' | 'SOUND' | 'BOTH';
```

### Siren modes

```typescript
export type SirenMode = 'AUTO' | 'MANUAL' | 'DISABLED';
```

### Image size

```python
IMG_SIZE = (224, 224)
```

### Detection classes

```python
CLASSES = ["BIRD", "SOUND"]
```

### Confidence threshold

```python
SEUIL = 0.70
```

---

## 🎯 Project Goal

Smart Scarecrow aims to provide a connected agricultural monitoring system that can:

- 🌾 Monitor agricultural areas
- 📷 Detect birds using camera images
- 🎙️ Detect relevant sounds
- 🧠 Process detection data locally
- 🚨 Trigger a deterrent system
- 🌐 Send events to a central backend
- 📊 Display information through a web dashboard
- 🗄️ Keep a history of detected events

The project brings together **embedded systems, machine learning, IoT, backend development, and web technologies** in one agricultural application. 🌱

---

## 🌿 Project Highlights

<p align="center">

🌾 **Smart Agriculture**  
📷 **Computer Vision**  
🎙️ **Audio Detection**  
🧠 **Machine Learning**  
🔌 **IoT & Embedded Systems**  
🚨 **Automatic Deterrence**  
🌐 **Real-Time Dashboard**  
📊 **Data Monitoring**

</p>

---

<p align="center">
  🌱 <b>Smart Scarecrow</b> — Technology for smarter and more connected agriculture. 🚜
</p>
