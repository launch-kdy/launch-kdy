<div align="center">

### 🌐 README Languages

[![한국어](https://img.shields.io/badge/한국어-blue?style=for-the-badge)](README.md)
[![English](https://img.shields.io/badge/English-red?style=for-the-badge)](README.en.md)
[![日本語](https://img.shields.io/badge/日本語-green?style=for-the-badge)](README.jp.md)

</div>

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

## 👨‍💻 개인 담당 업무

### 📱 Frontend (React Native)

#### 🏠 **HomeScreen (메인 대시보드) 개발**

**구현 기능:**
- WebSocket 기반 실시간 센서 데이터 수신 및 화면 표시
- Spring Boot API 연동하여 당일 제어 장비 작동 횟수 집계
- 60초 주기 자동 갱신 로직 구현
- 온도/습도/토양/조도 센서 데이터 시각화
- LED, 팬모터, 물펌프, 모션감지 작동 통계 표시
- 연결 상태 체크 및 로딩 화면 처리
- dayjs 라이브러리를 활용한 실시간 타임스탬프 표시

**기술 구현 내용:**
- WebSocket 커스텀 훅(`useWebSocket`) 연동으로 실시간 데이터 통신
- REST API 호출 및 데이터 필터링 로직 작성
- Flexbox 기반 반응형 그리드 레이아웃 디자인
- 센서별 색상 테마 적용 (온도: 빨강, 습도: 보라, 토양: 파랑, 조도: 초록)
- ActivityIndicator를 활용한 로딩 상태 UI 구현
- useEffect 훅으로 자동 갱신 및 메모리 누수 방지

---

#### 🌙 **다크모드 시스템 구현**

**구현 기능:**
- 라이트/다크 테마 토글 기능
- AsyncStorage 기반 사용자 테마 설정 저장
- Context API를 활용한 전역 테마 상태 관리
- 앱 전체 화면에 일관된 테마 적용

**기술 구현 내용:**
- `ThemeContext` 생성 및 Provider 패턴 구현
- AsyncStorage를 통한 테마 설정 영구 저장
- 동적 스타일 객체 생성으로 테마별 색상 변경
- 앱 재실행 시 저장된 테마 자동 복원

---

#### 🌐 **다국어 지원 시스템 구현**

**구현 기능:**
- 한국어, 영어, 일본어 3개 언어 지원
- 설정 화면에서 언어 변경 기능
- AsyncStorage 기반 언어 설정 저장
- 앱 전체 텍스트 다국어 처리

**기술 구현 내용:**
- i18n 언어 리소스 파일 구조 설계 및 구현
- `LanguageContext` 생성으로 전역 언어 상태 관리
- AsyncStorage를 통한 언어 설정 영구 저장
- 동적 텍스트 렌더링으로 실시간 언어 변경 반영
- 각 화면별 번역 리소스 매핑 및 적용

---

### 💻 Frontend (React Web)

#### 🏢 **CompanyProfile (기업 소개 페이지) 개발**

**구현 기능:**
- 스크롤 기반 인터랙티브 애니메이션 구현
- 브랜드 스토리텔링을 위한 섹션별 레이아웃 설계
- 라즈베리파이 하드웨어 소개 및 시스템 구조 시각화
- 반응형 이미지 갤러리 및 슬라이더 구현
- 실시간 스크롤 진행도 계산 및 애니메이션 트리거

**기술 구현 내용:**
- `useRef`와 `getBoundingClientRect()`를 활용한 스크롤 위치 추적
- `useState`로 섹션별 스크롤 진행도(progress) 상태 관리
- CSS Transform 및 Opacity를 활용한 부드러운 페이드인/슬라이드 애니메이션
- Marquee 효과로 동적 텍스트 애니메이션 구현
- 조건부 렌더링으로 섹션별 차별화된 애니메이션 적용
- passive 이벤트 리스너로 스크롤 성능 최적화

---

#### 🎨 **고급 CSS 애니메이션 및 스타일링**

**구현 기능:**
- Glassmorphism 스타일의 블러 효과 카드 디자인
- 그라데이션 배경 및 텍스트 클리핑 효과
- 키프레임 애니메이션으로 요소별 등장 효과
- 이미지 슬라이더 및 3D 회전 효과
- 날씨별 동적 배경 애니메이션 (낮/밤 모드)

**기술 구현 내용:**
- CSS Modules를 활용한 컴포넌트 스코프 스타일링
- `@keyframes`로 복잡한 애니메이션 시퀀스 구현
- `backdrop-filter`와 `blur()`를 활용한 유리 질감 효과
- `linear-gradient`와 `background-clip: text`로 그라데이션 텍스트 구현
- CSS 변수(`--variable`)를 활용한 동적 스타일 제어
- 마우스 hover 시 `transform: scale()` 및 `box-shadow` 변화 효과

---

#### 📊 **UserControl (센서 대시보드) 개발**

**구현 기능:**
- Spring Boot API 연동하여 센서 데이터 실시간 조회
- 온도/습도/토양/조도 센서별 카드형 UI 구현
- 모션 감지 통계 및 최근 감지 시간 표시
- 날씨 위젯 통합 및 시간대별 배경 애니메이션
- 센서 임계값에 따른 동적 스타일 변경

**기술 구현 내용:**
- Axios를 활용한 REST API 비동기 통신
- `useEffect` 훅으로 컴포넌트 마운트 시 데이터 로딩
- 배열 메서드(`map`, `filter`, `at()`)를 활용한 데이터 가공
- CSS Grid를 활용한 반응형 카드 레이아웃
- 조건부 클래스 바인딩으로 센서 상태별 시각적 피드백 제공
- 시간대별 날씨 위젯 배경 및 애니메이션 효과 전환

---

#### ✨ **특수 효과 및 인터랙션**

**구현 기능:**
- 별똥별 애니메이션 (밤 모드)
- 반딧불이 및 풀벌레 애니메이션 (낮/밤 모드)
- 갈대 흔들림 효과
- 클라우드 동기화 로더 애니메이션
- 햇살 반짝임 및 사이렌 점멸 효과

**기술 구현 내용:**
- `Array.from()`과 `map()`을 활용한 다수 애니메이션 요소 생성
- `Math.random()`으로 랜덤 위치 및 딜레이 설정
- CSS `animation-delay`와 `animation-duration`으로 비동기 애니메이션 구현
- SVG 기반 복잡한 벡터 애니메이션 (클라우드 로더)
- `radial-gradient`와 `filter`를 활용한 빛 효과
- `transform-origin`과 `rotate()`를 활용한 자연스러운 움직임 구현

---

### 💾 Backend (Spring Boot)

#### 🌱 **Growing 모듈 (환경 센서 데이터 API) 개발**

**구현 기능:**
- 환경 센서 데이터 전체 조회 API (`GET /growings`)
- 최근 7일간 센서 데이터 조회 API (`GET /growings/weekly`)
- 센서 데이터 역순 조회 API (`GET /growings/rev`)
- ResponseEntity 기반 예외 처리 및 HTTP 상태 코드 관리

**기술 구현 내용:**
- **Controller**: RESTful API 엔드포인트 설계 및 구현
- **Service**: 비즈니스 로직 레이어 분리
- **Mapper**: MyBatis 인터페이스를 통한 DB 접근
- **DTO**: LocalDateTime 기반 타임스탬프 처리
- Lombok `@RequiredArgsConstructor`로 의존성 주입
- SLF4J 로깅으로 요청/응답 추적

**API 엔드포인트:**

| Method | URI | 설명 | 반환 타입 |
|--------|-----|------|-----------|
| GET | `/growings` | 전체 센서 데이터 조회 | `List<GrowingDTO>` |
| GET | `/growings/weekly` | 최근 7일 데이터 조회 | `List<GrowingDTO>` |
| GET | `/growings/rev` | 센서 데이터 역순 조회 | `List<GrowingDTO>` |

**DTO 구조:**
```java
@Data
public class GrowingDTO {
  private int temperNum;           // 데이터 고유번호
  private float temper;            // 온도 (℃)
  private float humidity;          // 습도 (%)
  private float soilHumidity;      // 토양 수분 (%)
  private int illumination;        // 조도
  private LocalDateTime createDate; // 측정 시간
}
```

---

#### 🚨 **MotionBuzzer 모듈 (보안 및 장비 제어 API) 개발**

**구현 기능:**
- 모션 감지 로그 전체 조회 API (`GET /motions`)
- 감지 통계 및 최근 감지 시간 조회 API (`GET /motions/stats`)
- 당일 장비 제어 횟수 조회 API (`GET /motions/today`)
- 예외 처리 및 에러 메시지 표준화

**기술 구현 내용:**
- **Controller**: 보안 이벤트 및 장비 상태 조회 API 설계
- **Service**: 통계 집계 로직 구현
- **Mapper**: MyBatis를 통한 복잡한 쿼리 처리
- **DTO**: 통계용 필드(`detectedCount`, `lastDetected`) 추가 설계
- try-catch 블록으로 안정적인 에러 핸들링
- HTTP 상태 코드 기반 응답 처리

**API 엔드포인트:**

| Method | URI | 설명 | 반환 타입 |
|--------|-----|------|-----------|
| GET | `/motions` | 모션 감지 로그 전체 조회 | `List<MotionBuzzerDTO>` |
| GET | `/motions/stats` | 감지 횟수 + 최근 시간 통계 | `MotionBuzzerDTO` |
| GET | `/motions/today` | 오늘 장비 작동 횟수 조회 | `List<MotionBuzzerDTO>` |

**DTO 구조:**
```java
@Data
public class MotionBuzzerDTO {
  // 기본 로그 필드
  private int statusId;            // 상태 로그 ID
  private LocalDateTime timestamp; // 감지 시간
  private boolean motionDetected;  // 모션 감지 여부 (true/false)
  
  // 장비 제어 상태 필드
  private int fanMotor;            // 팬 모터 작동 횟수
  private int waterPump;           // 물펌프 작동 횟수
  private int ledLight;            // LED 작동 횟수
  
  // 통계용 필드
  private Integer detectedCount;   // 총 감지 횟수
  private LocalDateTime lastDetected; // 최근 감지 시간
}
```

**주요 특징:**
- 단일 DTO로 로그 조회 + 통계 조회 멀티 용도 처리
- boolean 타입으로 모션 감지 상태 명확하게 표현
- 장비별 작동 횟수를 하나의 응답으로 통합 관리
- 모바일 앱 HomeScreen의 당일 통계 데이터 제공 API 역할

---

#### 🔧 **백엔드 아키텍처 설계 기여**

**구현 내용:**
- **계층형 아키텍처** 적용: Controller → Service → Mapper → DB
- **DTO 기반 데이터 전송**: Entity 노출 방지 및 API 응답 최적화
- **MyBatis Mapper 인터페이스** 설계로 SQL 분리 및 유지보수성 향상
- **Lombok 활용**: 보일러플레이트 코드 최소화
- **RESTful API 설계 원칙** 준수: 리소스 중심 URI, HTTP 메서드 적절한 사용
- **예외 처리 표준화**: ResponseEntity로 일관된 에러 응답 제공

**코드 품질:**
- SLF4J 로거를 통한 디버깅 및 모니터링 지원
- 생성자 주입 방식으로 불변성 보장
- try-catch 블록으로 안정적인 예외 처리
- HTTP 상태 코드(200 OK, 500 Internal Server Error) 명확한 구분

---

## 🧠 Tech Stack

| 분류 | 기술 스택 |
|------|-----------|
| **IoT / Embedded** | Python 3.11, RPi.GPIO, spidev, adafruit_dht, asyncio, websockets |
| **Backend** | Spring Boot (Java 17), MyBatis, MariaDB |
| **Frontend (Web)** | React (Vite), Chart.js, Axios, CSS Modules |
| **Mobile App** | React Native, Expo, SecureStore, WebSocket, AsyncStorage |
| **Infra** | Raspberry Pi 3B+, GitHub, VS Code Remote SSH |
| **Design** | Figma, Glassmorphism / Apple-style UI |

---

## 🗂️ Database Design (ERD)

<img width="1959" height="720" alt="Image" src="https://github.com/user-attachments/assets/b3fc07da-fefc-417a-8c74-a7fddd359533" />

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
