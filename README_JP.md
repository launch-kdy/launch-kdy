# 🌱 root Smart Farm IoT System

**第1段階 IoT → 第2段階 Web → 第3段階 モバイルアプリ**  
段階的に拡張された統合スマートファーム管理システム

<img width="1818" height="763" alt="Image" src="https://github.com/user-attachments/assets/74a7ab88-bedf-4768-b77d-3128926f191a" />
---

## 🔗 Repository Links

| 区分 | 説明 | リンク |
|------|------|------|
| 💾 **Backend (1차)** | Spring Boot ベースのバックエンドサーバーおよび API | [🔗 backend_root](https://github.com/launch-kdy/backend_root) |
| 💻 **Web Frontend (2차)** | React ベースの環境モニタリング Web ダッシュボード | [🔗 frontend_root](https://github.com/launch-kdy/frontend_root) |
| 📱 **Mobile App (3차)** | React-Native ベースのスマートファーム モバイルアプリ | [🔗 frontend_root_rn](https://github.com/launch-kdy/frontend_root_rn) |

---

## 📘 Project Overview

**root** は植物栽培のための **IoT ベースのスマートファーム環境制御・モニタリングシステム** です。  
Raspberry Pi によるセンサーデータの収集と自動制御から始まり、  
**Spring Boot** ベースのバックエンドと **React / React-Native** を通して、  
Web・モバイル環境の両方で **リアルタイムに農場の状態を管理・制御できる統合プラットフォーム** を実現しました。

### チーム名: root

**意味:**
- **r**（緑の芽）→ スマートファーム開発の出発点とチームメンバーの成長を象徴 `#72A84F`
- **o**（ギア）→ メンバーの協力と努力が噛み合う様子を象徴 `#505640`
- **o**（回路）→ IoT・データ技術、スマート制御、プログラミング知識を象徴 `#72A84F`
- **t**（根）→ 農業発展への貢献、植物の健康的な基盤を象徴 `#B3703E`

---

## 🧩 System Structure
```
📡 IoT デバイス (第1段階)
 ├─ DHT22（温湿度）
 ├─ FC-28（土壌水分）
 ├─ LDR（照度）
 ├─ PIR + ブザー（セキュリティ）
 ├─ ファンモーター / ウォーターポンプ / LED 自動制御
 └─ Python + MariaDB + WebSocket

💻 Web プラットフォーム (第2段階)
 ├─ Spring Boot REST API
 ├─ React ダッシュボード (Chart.js)
 ├─ IoT データの可視化 / 制御ログ管理

📱 モバイルアプリ (第3段階)
 ├─ React Native + Expo
 ├─ リアルタイム制御 (WebSocket)
 ├─ 環境データおよび通知 UI
```

---

## ⚙️ 第1段階: IoT 自動制御システム (Raspberry Pi)

### 📍 主要機能

| 機能 | 説明 | 周期 / 条件 |
|------|------|-------------|
| 🌡️ **温度管理** | DHT22 センサーで温度測定 → DB 保存 | 60分ごと |
| 💧 **湿度管理** | 空気中湿度を測定 → DB 保存 | 60分ごと |
| 🌱 **土壌水分管理** | FC-28 センサーで水分測定 → ポンプ自動制御 | 60分ごと |
| ☀️ **照度管理** | LDR センサーで光強度を測定 → LED 自動点灯 | リアルタイム |
| 🎐 **ファンモーター制御** | 28°C 以上で自動作動 | 温度条件 |
| 🚨 **モーション検知・ブザー警報** | PIR センサーで異常検知 | リアルタイム |
| 🌐 **リアルタイム遠隔制御 (WebSocket)** | Web/アプリで手動・自動制御およびデータ確認 | 常時接続 |

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
 │   │   └─ device/         # デバイス状態 (ファン/ポンプ/LED/モーション)
 │   └─ RootApplication.java
 └─ src/main/resources/
     ├─ mapper/             # MyBatis XML
     └─ application.yml
```

---

## 📱 第3段階: モバイルアプリ (React Native)

### 主な機能
- **Expo Router** ベースのネイティブアプリ構造  
- **WebSocket** によるリアルタイム制御  
- **SecureStore** を利用したログイン・権限維持  
- 一日の作動統計 / 通知 / 制御ログの確認  

---

## 🧠 Tech Stack

| 分類 | 技術スタック |
|------|---------------|
| **IoT / Embedded** | Python 3.11, RPi.GPIO, spidev, adafruit_dht, asyncio, websockets |
| **Backend** | Spring Boot (Java 17), MyBatis, MariaDB |
| **Frontend** | React (Vite), Chart.js, Axios |
| **Mobile App** | React Native, Expo, SecureStore, WebSocket |
| **Infra** | Raspberry Pi 3B+, GitHub, VS Code Remote SSH |
| **Design** | Figma, Glassmorphism / Apple-style UI |

---

## 🚀 成果および今後の展開

| 段階 | 主な成果 | 備考 |
|------|-----------|------|
| **第1段階** | Raspberry Pi IoT 自動制御システム構築 | センサー・モーター制御完了 |
| **第2段階** | Spring-React Web統合 / DB連携 | 環境データ可視化 |
| **第3段階** | WebSocket リアルタイム遠隔制御アプリ完成 | Expoアプリ動作確認 |
| **今後** | クラウドDB (AWS RDS / Firebase) 移行, AI生育条件予測 | 研究開発予定 |

---

## 👣 License & Contact

### 🏁 License

本プロジェクトは **root チームの共同開発成果** の一部を  
**学習およびポートフォリオ目的** で再構成したものです。

- サーバ設定、DB接続情報、APIキーなどの機密情報は非公開です。  
- 商用利用および二次配布は禁止されています。  
- 本リポジトリの公開目的は **個人スキルおよび開発過程の共有** のみに限定されます。

**© 2025 DOYOUNG KIM (金 度榮) (root Team)**  
All Rights Reserved.

---

### 🧠 Credits

**Project Team:** root 🌱  
**Development Period:** 2025 09 11 ~ 2025 11 14  
**Institution:** K-Digital Training  
**Project Goal:** IoT スマートファームを基盤とした作物環境モニタリングおよび自動制御システム  

---

### 📬 Contact

| 区分 | 内容 |
|------|------|
| 👤 **Name** | DOYOUNG KIM (金 度榮) |
| 💻 **GitHub** | [github.com/launch-kdy](https://github.com/launch-kdy) |
| 💌 **Email** | bre.xx.bpm@gmail.com |
| 🏫 **Affiliation** | 桜美林大学 / K-Digital Training |

---

🔒 **本プロジェクトはポートフォリオ用に限定公開されたバージョンです。**  
実際の運用環境に関するDB、APIキー等のセキュリティ関連情報は除外されています。  
技術構造、設計、および主要コードの一部のみを公開しています。
