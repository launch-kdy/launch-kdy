<div align="center">

### 🌐 README Languages

[![한국어](https://img.shields.io/badge/한국어-blue?style=for-the-badge)](README.md)
[![English](https://img.shields.io/badge/English-red?style=for-the-badge)](README.en.md)
[![日本語](https://img.shields.io/badge/日本語-green?style=for-the-badge)](README.jp.md)

</div>

# 🌱 スマートファーム IoT システム

**第1段階 IoT → 第2段階 Web → 第3段階 モバイルアプリ**  
段階的に拡張された統合スマートファーム管理システム

<img width="1818" height="763" alt="Image" src="https://github.com/user-attachments/assets/74a7ab88-bedf-4768-b77d-3128926f191a" />

---

## 🔗 Repository Links

| 区分 | 説明 | リンク |
|------|------|------|
| 💾 **Backend (第1段階)** | Spring Boot ベースのバックエンドサーバー及びAPI | [🔗 backend_root](https://github.com/launch-kdy/backend_root) |
| 💻 **Web Frontend (第2段階)** | React ベースの環境モニタリング Web ダッシュボード | [🔗 frontend_root](https://github.com/launch-kdy/frontend_root) |
| 📱 **Mobile App (第3段階)** | React-Native ベースのスマートファーム モバイルアプリ | [🔗 frontend_root_rn](https://github.com/launch-kdy/frontend_root_rn) |

---

## 📘 Project Overview

**root** は植物栽培のための **IoT ベースのスマートファーム環境制御及びモニタリングシステム** です。  
Raspberry Pi を利用したセンサーデータ収集と自動制御から始まり、  
**Spring Boot** ベースのバックエンドと **React / React-Native** を通じて  
Web・モバイル環境で **リアルタイムに農場の状態を管理・制御** できる統合プラットフォームを実装しました。

### チーム名: root

**意味:**
- **r** (緑の新芽) → スマートファーム開発の始まりとチームメンバーの成長を象徴 `#72A84F`
- **o** (歯車) → 噛み合って回るチームメンバーの協力と努力を象徴 `#505640`
- **o** (回路) → IoT・データ技術、スマート制御、コーディングなどの技術的知識を象徴 `#72A84F`
- **t** (根) → 農業発展に貢献し、植物の健全な基盤であり根を張った安定的基盤を象徴 `#B3703E`

---

## 🧩 System Structure
```
📡 IoT Device (第1段階)
 ├─ DHT22 (温湿度)
 ├─ FC-28 (土壌水分)
 ├─ LDR (照度)
 ├─ PIR + ブザー (セキュリティ)
 ├─ ファンモーター / ウォーターポンプ / LED 自動制御
 └─ Python + MariaDB + WebSocket

💻 Web Platform (第2段階)
 ├─ Spring Boot REST API
 ├─ React Dashboard (Chart.js)
 ├─ IoT データ可視化 / 制御ログ管理

📱 Mobile App (第3段階)
 ├─ React Native + Expo
 ├─ リアルタイム制御 (WebSocket)
 ├─ 環境データ及び通知 UI
```

---

## ⚙️ 第1段階: IoT 自動制御システム (Raspberry Pi)

### 📍 主要機能

| 機能 | 説明 | 周期/条件 |
|------|------|-----------|
| 🌡️ **温度管理** | DHT22 センサーで温度測定 → DB 保存 | 60分毎 |
| 💧 **湿度管理** | 空気中の湿度測定 → DB 保存 | 60分毎 |
| 🌱 **土壌水分管理** | FC-28 センサーで土壌水分測定 → ポンプ自動制御 | 60分毎 |
| ☀️ **照度管理** | LDR センサーで光の強さ測定 → LED 自動点灯 | リアルタイム |
| 🎐 **ファンモーター制御** | 28°C 以上で自動作動 | 温度条件 |
| 🚨 **モーション検知及びブザーアラーム** | PIR センサーで異常な動きを検知 | リアルタイム |
| 🌐 **リアルタイム遠隔制御 (WebSocket)** | Web/アプリから手動/自動制御及びデータ照会 | 常時接続 |

### 🔌 センサー & 部品構成

| センサー/装置 | データ性質 | MCP3008 必要有無 | 説明 |
|-----------|-------------|-------------------|------|
| **DHT22** (温湿度) | デジタル | ❌ 不要 | GPIO で直接読み取り |
| **FC-28** (土壌水分) | アナログ | ✅ 必要 | MCP3008 経由で変換 |
| **LDR** (照度) | アナログ | ✅ 必要 | MCP3008 経由で変換 (spi.xfer2() 使用) |
| **PIR** センサー (モーション) | デジタル | ❌ 不要 | 動き検知 → 0/1 |
| **ブザー** | デジタル | ❌ 不要 | GPIO 出力制御 |
| **ファンモーター** | PWM | ❌ 不要 | L9110S ドライバー使用 |
| **MCP3008** | - | - | **デジタル変換器(ADC)** |

> 💡 **重要説明:**  
> 「アナログセンサーは Raspberry Pi が直接読み取れないため、MCP3008 を経由してデータを受け取り、デジタルセンサーは直接接続して読み取ることができました。」

### ⚡ GPIO ピン割り当て

| センサー / 装置 | GPIO ピン | 用途 | 制御方式 |
|-------------|---------|------|-----------|
| DHT22 | 14 | 温湿度測定 | デジタル信号 |
| FC-28 | MCP3008 CH0 | 土壌水分測定 | ADC 変換 |
| LDR | MCP3008 CH1 | 照度測定 | ADC 変換 |
| LED | 2 | 照明制御 | デジタル出力 |
| PIR (HC-SR501) | 17 | モーション検知 | デジタル入力 |
| ブザー | 3 | アラーム出力 | PWM 周波数 |
| ファンモーター | 22, 27 | 換気制御 | PWM 速度 (L9110S) |

### 🔄 制御ロジック

| センサー | 閾値 | 動作 | 制御装置 |
|------|--------|------|-----------|
| 照度センサー | < 250 | LED ON | GPIO 2 |
| 照度センサー | ≥ 250 | LED OFF | GPIO 2 |
| 温度センサー | ≥ 28°C | ファン ON | GPIO 22, 27 |
| 温度センサー | < 28°C | ファン OFF | GPIO 22, 27 |
| PIR センサー | 検知 | ブザー作動 | GPIO 3 |
| PIR センサー | 未検知 | ブザー OFF | - |

### 🧵 スレッド別動作周期 (統合システム)

| スレッド名 | 周期 | センサー | 制御装置 | DB 保存 | 主要機能 |
|----------|------|------|-----------|---------|-----------|
| **照度 + LED** | 2秒 | LDR 照度センサー | LED | ❌ | リアルタイム照明制御 |
| **環境 + DB** | 60分 | DHT22, FC-28 | ファンモーター | ✅ | 環境モニタリング / データ保存 |
| **モーション + ブザー** | 1秒 | PIR | ブザー | ❌ | リアルタイムセキュリティ通知 |
| **WebSocket** | リアルタイム | - | 全体 | ❌ | 遠隔制御通信 |

### 📁 主要ファイル構造

| ファイル名 | 役割 | 説明 |
|--------|------|------|
| `multi_sensor.py` | センサーデータ収集 | 温湿度、照度、土壌水分データの収集及び変換 |
| `multi_control.py` | 自動制御 | LED、ファン、ウォーターポンプ自動制御ロジック |
| `motion_detector.py` | セキュリティ機能 | PIR センサーベースの動き検知及びブザー通知 |
| `fan_motor.py` | ファン制御 | L9110S ドライバーベースのファンモーター制御 |
| `config_manager.py` | 設定管理 | 作物別閾値 / 自動・手動モード管理 |
| `websocket_server.py` | リアルタイム通信 | クライアント(Web/アプリ)との WebSocket 通信 |
| `integrated_system.py` | **統合システムメイン** | 全てのスレッド(センサー・制御・通知・通信)統合管理 |

---

## 💻 第2段階: Web ベースモニタリングシステム (Spring + React)

### 📦 Backend (Spring Boot)

#### プロジェクト構造
```
backend_root/
 ├─ src/main/java/com/root/
 │   ├─ common/
 │   │   ├─ config/         # Swagger, MyBatis, CORS
 │   │   ├─ exception/      # GlobalExceptionHandler
 │   │   └─ util/
 │   ├─ domain/
 │   │   ├─ environment/    # 環境データ (温度/湿度/土壌/照度)
 │   │   │   ├─ controller/
 │   │   │   ├─ service/
 │   │   │   ├─ repository/
 │   │   │   └─ dto/
 │   │   └─ device/         # 装置状態 (ファン/ポンプ/LED/モーション)
 │   └─ RootApplication.java
 └─ src/main/resources/
     ├─ mapper/             # MyBatis XML
     └─ application.yml
```

#### 主要機能
- **REST API**: `/api/growings`, `/api/devices`
- **MyBatis Mapper** ベースの CRUD
- **データ統計** / 期間別照会機能
- **Swagger/OpenAPI** ドキュメント化

### 🖥️ Frontend (React)

- **Chart.js** ベースの環境データ可視化
- **ダッシュボード型 UI**: 温度・湿度・照度リアルタイム照会
- 装置状態ログ / 作動回数 / 制御ボタン UI

---

## 📱 第3段階: モバイルアプリ (React Native)

### 主要機能
- **Expo Router** ベースのネイティブアプリ構造
- **WebSocket** リアルタイム通信による遠隔制御
- **SecureStore** ログイン及びユーザー権限維持
- 日別作動統計 / 通知 / 制御ログ確認

### 主要画面

| 画面 | 説明 |
|------|------|
| 🌡️ **Home** | センサーデータのリアルタイム表示 |
| ⚙️ **Control** | ファン、LED、ポンプの手動・自動制御 |
| 📊 **Stats** | 日別作動回数及び DB 連動チャート |
| 🔔 **Alert** | モーション検知時のリアルタイム通知 |

---

## 🧠 Tech Stack

| 分類 | 技術スタック |
|------|-----------|
| **IoT / Embedded** | Python 3.11, RPi.GPIO, spidev, adafruit_dht, asyncio, websockets |
| **Backend** | Spring Boot (Java 17), MyBatis, MariaDB |
| **Frontend** | React (Vite), Chart.js, Axios |
| **Mobile App** | React Native, Expo, SecureStore, WebSocket |
| **Infra** | Raspberry Pi 3B+, GitHub, VS Code Remote SSH |
| **Design** | Figma, Glassmorphism / Apple-style UI |

---

## 🗂️ Database Design (ERD)

<img width="1959" height="720" alt="Image" src="https://github.com/user-attachments/assets/b3fc07da-fefc-417a-8c74-a7fddd359533" />

### 主要テーブル

#### 📊 GROWING_ENVIRONMENT (環境データ)
| カラム | タイプ | 説明 |
|------|------|------|
| TEMPER_NUM | INT (PK, AUTO_INCREMENT) | 固有番号 |
| TEMPER | FLOAT | 温度 (℃) |
| HUMIDITY | FLOAT | 湿度 (%) |
| SOIL_HUMIDITY | FLOAT | 土壌水分 (%) |
| ILLUMINATION | INT | 照度 |
| CREATE_DATE | DATETIME | データ保存時刻 |

#### ⚙️ DEVICE_STATUS (制御装置状態)
| カラム | タイプ | 説明 |
|------|------|------|
| STATUS_ID | INT (PK, AUTO_INCREMENT) | 状態ログ ID |
| TIMESTAMP | DATETIME | 記録時刻 |
| MOTION_DETECTED | TINYINT(1) | PIR 検知有無 (0/1) |
| FAN_MOTOR | TINYINT(1) | ファン状態 (0/1) |
| WATER_PUMP | TINYINT(1) | ポンプ状態 (0/1) |
| LED_LIGHT | TINYINT(1) | 照明状態 (0/1) |

---

## 🚀 成果及び今後の拡張

| 段階 | 主要成果 | 備考 |
|------|-----------|------|
| **第1段階** | Raspberry Pi IoT 自動制御システム構築 | センサー・モーター制御完成 |
| **第2段階** | Spring-React Web 統合 / DB 連動 | 環境データ可視化 |
| **第3段階** | WebSocket ベースリアルタイム遠隔制御アプリ完成 | Expo アプリ実行成功 |
| **今後の計画** | クラウド DB (AWS RDS / Firebase) 移管、AI 生育条件予測 | 研究及び拡張予定 |

---

## 👣 License & Contact

### 🏁 License

本プロジェクトは **root チーム協業プロジェクト** の一部を  
**学習及びポートフォリオ目的** で再構成して公開しました。

- 機密コード(サーバー設定、DB アクセス情報、API Key など)は全て非公開処理されています。
- 商業的使用及び二次配布は禁止されています。
- 本リポジトリの公開目的は **個人能力及び開発過程の共有** に限定されます。

**© 2025 DOYOUNG KIM (金 度榮) (root Team)**  
All Rights Reserved.

---

### 🧠 Credits

**Project Team:** root 🌱

**Development Period:** 2025 09 11 ~ 2025 11 14

**Institution:** K-Digital Training

**Project Goal:** スマートファーム IoT ベースの農作物環境モニタリング及び自動制御システム

---

### 📬 Contact

| 区分 | 内容 |
|------|------|
| 👤 **Name** | DOYOUNG KIM (金 度榮) |
| 💻 **GitHub** | [github.com/launch-kdy](https://github.com/launch-kdy) |
| 💌 **Email** | bre.xx.bpm@gmail.com |
| 🏫 **Affiliation** | 桜美林大学 / K-Digital Training |

---

🔒 **本プロジェクトはポートフォリオ用制限公開版であり、**  
実際の運用環境の DB、API Key などセキュリティ関連情報は除外されています。  
技術構造、設計、主要コードの一部のみ公開されています。
