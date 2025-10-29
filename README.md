# 🌱 root Smart Farm IoT System

**1차 IoT → 2차 웹 → 3차 모바일 앱**  
단계별로 확장된 통합 스마트팜 관리 시스템

<img width="1818" height="763" alt="Image" src="https://github.com/user-attachments/assets/74a7ab88-bedf-4768-b77d-3128926f191a" />
---

## 🔗 Repository Links

| 구분 | 설명 | 링크 |
|------|------|------|
| 💾 **Backend (1차)** | Spring Boot 기반 백엔드 서버 및 API | [🔗 backend_root](https://github.com/launch-kdy/backend_root) |
| 💻 **Web Frontend (2차)** | React 기반 환경 모니터링 웹 대시보드 | [🔗 frontend_root](https://github.com/launch-kdy/frontend_root) |
| 📱 **Mobile App (3차)** | React-Native 기반 스마트팜 모바일 앱 | [🔗 frontend_root_rn](https://github.com/launch-kdy/frontend_root_rn) |

---

## 📘 Project Overview

**root**는 식물 재배를 위한 **IoT 기반 스마트팜 환경 제어 및 모니터링 시스템**입니다.  
Raspberry Pi를 이용한 센서 데이터 수집과 자동 제어를 시작으로,  
**Spring Boot** 기반 백엔드와 **React / React-Native**를 통해  
웹·모바일 환경에서 **실시간으로 농장 상태를 관리하고 제어**할 수 있는 통합 플랫폼을 구현했습니다.

### 팀명: root

**의미:**
- **r** (초록 새싹) → 스마트팜 개발의 시작과 팀원의 성장을 상징 `#72A84F`
- **o** (톱니바퀴) → 맞물려 돌아가는 팀원의 협력과 노력을 상징 `#505640`
- **o** (회로) → IoT·데이터 기술, 스마트 제어, 코딩 등의 기술적 지식을 상징 `#72A84F`
- **t** (뿌리) → 농업 발전에 기여하며, 식물의 건강한 터전이자 뿌리내린 안정적 기반을 상징 `#B3703E`

---

## 🧩 System Structure
```
📡 IoT Device (1차)
 ├─ DHT22 (온습도)
 ├─ FC-28 (토양수분)
 ├─ LDR (조도)
 ├─ PIR + 부저 (보안)
 ├─ 팬모터 / 물펌프 / LED 자동제어
 └─ Python + MariaDB + WebSocket

💻 Web Platform (2차)
 ├─ Spring Boot REST API
 ├─ React Dashboard (Chart.js)
 ├─ IoT 데이터 시각화 / 제어 로그 관리

📱 Mobile App (3차)
 ├─ React Native + Expo
 ├─ 실시간 제어 (WebSocket)
 ├─ 환경 데이터 및 알림 UI
```

---

## ⚙️ 1차: IoT 자동 제어 시스템 (Raspberry Pi)

### 📍 핵심 기능

| 기능 | 설명 | 주기/조건 |
|------|------|-----------|
| 🌡️ **온도관리** | DHT22 센서로 온도 측정 → DB 저장 | 60분마다 |
| 💧 **습도관리** | 공기 중 습도 측정 → DB 저장 | 60분마다 |
| 🌱 **토양수분관리** | FC-28 센서로 토양 수분 측정 → 펌프 자동 제어 | 60분마다 |
| ☀️ **조도관리** | LDR 센서로 빛 세기 측정 → LED 자동 점등 | 실시간 |
| 🎐 **팬 모터 제어** | 28°C 이상 시 자동 작동 | 온도조건 |
| 🚨 **모션 감지 및 부저 알람** | PIR 센서로 이상 움직임 감지 | 실시간 |
| 🌐 **실시간 원격 제어 (WebSocket)** | 웹/앱에서 수동/자동 제어 및 데이터 조회 | 상시 연결 |

### 🔌 센서 & 부품 구성

| 센서/장치 | 데이터 성격 | MCP3008 필요 여부 | 설명 |
|-----------|-------------|-------------------|------|
| **DHT22** (온습도) | 디지털 | ❌ 필요 없음 | GPIO로 바로 읽음 |
| **FC-28** (토양수분) | 아날로그 | ✅ 필요함 | MCP3008 통해 변환 |
| **LDR** (조도) | 아날로그 | ✅ 필요함 | MCP3008 통해 변환 (spi.xfer2() 사용) |
| **PIR** 센서 (모션) | 디지털 | ❌ 필요 없음 | 움직임 감지 → 0/1 |
| **부저** | 디지털 | ❌ 필요 없음 | GPIO 출력 제어 |
| **팬 모터** | PWM | ❌ 필요 없음 | L9110S 드라이버 사용 |
| **MCP3008** | - | - | **디지털 변환기(ADC)** |

> 💡 **핵심 설명:**  
> "아날로그 센서는 라즈베리파이가 직접 못 읽기 때문에 MCP3008을 거쳐서 데이터를 받아왔고, 디지털 센서는 바로 연결해서 읽을 수 있었습니다."

### ⚡ GPIO 핀 할당

| 센서 / 장치 | GPIO 핀 | 용도 | 제어 방식 |
|-------------|---------|------|-----------|
| DHT22 | 14 | 온습도 측정 | 디지털 신호 |
| FC-28 | MCP3008 CH0 | 토양수분 측정 | ADC 변환 |
| LDR | MCP3008 CH1 | 조도 측정 | ADC 변환 |
| LED | 2 | 조명 제어 | 디지털 출력 |
| PIR (HC-SR501) | 17 | 모션 감지 | 디지털 입력 |
| 부저 | 3 | 알람 출력 | PWM 주파수 |
| 팬 모터 | 22, 27 | 환기 제어 | PWM 속도 (L9110S) |

### 🔄 제어 로직

| 센서 | 임계값 | 동작 | 제어 장치 |
|------|--------|------|-----------|
| 조도센서 | < 250 | LED ON | GPIO 2 |
| 조도센서 | ≥ 250 | LED OFF | GPIO 2 |
| 온도센서 | ≥ 28°C | 팬 ON | GPIO 22, 27 |
| 온도센서 | < 28°C | 팬 OFF | GPIO 22, 27 |
| PIR센서 | 감지됨 | 부저 울림 | GPIO 3 |
| PIR센서 | 미감지 | 부저 OFF | - |

### 🧵 스레드별 동작 주기 (통합 시스템)

| 스레드명 | 주기 | 센서 | 제어 장치 | DB 저장 | 주요 기능 |
|----------|------|------|-----------|---------|-----------|
| **조도 + LED** | 2초 | LDR 조도센서 | LED | ❌ | 실시간 조명 제어 |
| **환경 + DB** | 60분 | DHT22, FC-28 | 팬 모터 | ✅ | 환경 모니터링 / 데이터 저장 |
| **모션 + 부저** | 1초 | PIR | 부저 | ❌ | 실시간 보안 알림 |
| **WebSocket** | 실시간 | - | 전체 | ❌ | 원격 제어 통신 |

### 📁 주요 파일 구조

| 파일명 | 역할 | 설명 |
|--------|------|------|
| `multi_sensor.py` | 센서 데이터 수집 | 온습도, 조도, 토양 수분 데이터 수집 및 변환 |
| `multi_control.py` | 자동 제어 | LED, 팬, 물펌프 자동 제어 로직 |
| `motion_detector.py` | 보안 기능 | PIR 센서 기반 움직임 감지 및 부저 알림 |
| `fan_motor.py` | 팬 제어 | L9110S 드라이버 기반 팬 모터 제어 |
| `config_manager.py` | 설정 관리 | 작물별 임계값 / 자동·수동 모드 관리 |
| `websocket_server.py` | 실시간 통신 | 클라이언트(Web/앱)와 WebSocket 통신 |
| `integrated_system.py` | **통합 시스템 메인** | 모든 스레드(센서·제어·알림·통신) 통합 관리 |

---

## 💻 2차: 웹 기반 모니터링 시스템 (Spring + React)

### 📦 Backend (Spring Boot)

#### 프로젝트 구조
```
backend_root/
 ├─ src/main/java/com/root/
 │   ├─ common/
 │   │   ├─ config/         # Swagger, MyBatis, CORS
 │   │   ├─ exception/      # GlobalExceptionHandler
 │   │   └─ util/
 │   ├─ domain/
 │   │   ├─ environment/    # 환경데이터 (온도/습도/토양/조도)
 │   │   │   ├─ controller/
 │   │   │   ├─ service/
 │   │   │   ├─ repository/
 │   │   │   └─ dto/
 │   │   └─ device/         # 장비상태 (팬/펌프/LED/모션)
 │   └─ RootApplication.java
 └─ src/main/resources/
     ├─ mapper/             # MyBatis XML
     └─ application.yml
```

#### 주요 기능
- **REST API**: `/api/growings`, `/api/devices`
- **MyBatis Mapper** 기반 CRUD
- **데이터 통계** / 기간별 조회 기능
- **Swagger/OpenAPI** 문서화

### 🖥️ Frontend (React)

- **Chart.js** 기반 환경 데이터 시각화
- **대시보드형 UI**: 온도·습도·조도 실시간 조회
- 장비 상태 로그 / 작동 횟수 / 제어 버튼 UI

---

## 📱 3차: 모바일 앱 (React Native)

### 주요 기능
- **Expo Router** 기반 네이티브 앱 구조
- **WebSocket** 실시간 통신으로 원격 제어
- **SecureStore** 로그인 및 사용자 권한 유지
- 하루 작동 통계 / 알림 / 제어 로그 확인

### 핵심 스크린

| 화면 | 설명 |
|------|------|
| 🌡️ **Home** | 센서 데이터 실시간 표시 |
| ⚙️ **Control** | 팬, LED, 펌프 수동·자동 제어 |
| 📊 **Stats** | 하루 작동 횟수 및 DB 연동 차트 |
| 🔔 **Alert** | 모션 감지 시 실시간 알림 |

---

## 🧠 Tech Stack

| 분류 | 기술 스택 |
|------|-----------|
| **IoT / Embedded** | Python 3.11, RPi.GPIO, spidev, adafruit_dht, asyncio, websockets |
| **Backend** | Spring Boot (Java 17), MyBatis, MariaDB |
| **Frontend** | React (Vite), Chart.js, Axios |
| **Mobile App** | React Native, Expo, SecureStore, WebSocket |
| **Infra** | Raspberry Pi 3B+, GitHub, VS Code Remote SSH |
| **Design** | Figma, Glassmorphism / Apple-style UI |

---

## 🗂️ Database Design (ERD)

![ERD Diagram](이미지경로/erd-diagram.png)

### 주요 테이블

#### 📊 GROWING_ENVIRONMENT (환경 데이터)
| 컬럼 | 타입 | 설명 |
|------|------|------|
| TEMPER_NUM | INT (PK, AUTO_INCREMENT) | 고유번호 |
| TEMPER | FLOAT | 온도 (℃) |
| HUMIDITY | FLOAT | 습도 (%) |
| SOIL_HUMIDITY | FLOAT | 토양수분 (%) |
| ILLUMINATION | INT | 조도 |
| CREATE_DATE | DATETIME | 데이터 저장 시간 |

#### ⚙️ DEVICE_STATUS (제어 장비 상태)
| 컬럼 | 타입 | 설명 |
|------|------|------|
| STATUS_ID | INT (PK, AUTO_INCREMENT) | 상태 로그 ID |
| TIMESTAMP | DATETIME | 기록 시간 |
| MOTION_DETECTED | TINYINT(1) | PIR 감지 여부 (0/1) |
| FAN_MOTOR | TINYINT(1) | 팬 상태 (0/1) |
| WATER_PUMP | TINYINT(1) | 펌프 상태 (0/1) |
| LED_LIGHT | TINYINT(1) | 조명 상태 (0/1) |

---

## 🚀 성과 및 향후 확장

| 단계 | 주요 성과 | 비고 |
|------|-----------|------|
| **1차** | 라즈베리파이 IoT 자동제어 시스템 구축 | 센서·모터 제어 완성 |
| **2차** | Spring-React 웹 통합 / DB 연동 | 환경 데이터 시각화 |
| **3차** | WebSocket 기반 실시간 원격 제어 앱 완성 | Expo 앱 실행 성공 |
| **향후 계획** | 클라우드 DB (AWS RDS / Firebase) 이관, AI 생육 조건 예측 | 연구 및 확장 예정 |

---

## 👣 License & Contact

### 🏁 License

본 프로젝트는 **root 팀 협업 프로젝트**의 일부를  
**학습 및 포트폴리오 목적**으로 재구성하여 공개하였습니다.

- 민감한 코드(서버 설정, DB 접근 정보, API Key 등)는 모두 비공개 처리되었습니다.
- 상업적 사용 및 2차 배포는 금지됩니다.
- 본 저장소의 공개 목적은 **개인 역량 및 개발 과정 공유**에 한정됩니다.

**© 2025 DOYOUNG KIM (金 度榮) (root Team)**  
All Rights Reserved.

---

### 🧠 Credits

**Project Team:** root 🌱

**Development Period:** 2025 09 11 ~ 2025 11 14

**Institution:** K-Digital Training

**Project Goal:** 스마트팜 IoT 기반 농작물 환경 모니터링 및 자동 제어 시스템

---

### 📬 Contact

| 구분 | 내용 |
|------|------|
| 👤 **Name** | DOYOUNG KIM (金 度榮) |
| 💻 **GitHub** | [github.com/launch-kdy](https://github.com/launch-kdy) |
| 💌 **Email** | bre.xx.bpm@gmail.com |
| 🏫 **Affiliation** | 桜美林大学 / K-Digital Training |

---

🔒 **본 프로젝트는 포트폴리오용 제한 공개 버전으로,**  
실제 운영 환경의 DB, API Key 등 보안 관련 정보는 제외되어 있습니다.  
기술 구조, 설계, 핵심 코드 일부만 공개되어 있습니다.
