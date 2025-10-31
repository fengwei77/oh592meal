# 592Meal - 街邊小吃訂餐平台

[![Laravel](https://img.shields.io/badge/Laravel-12-FF2D20?style=flat&logo=laravel)](https://laravel.com)
[![PHP](https://img.shields.io/badge/PHP-8.4-777BB4?style=flat&logo=php)](https://php.net)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-18-316192?style=flat&logo=postgresql)](https://postgresql.org)
[![Livewire](https://img.shields.io/badge/Livewire-3-FB70A9?style=flat&logo=livewire)](https://livewire.laravel.com)

> 專為台灣街邊小吃攤商與企業駐點服務設計的訂餐平台

## 📋 目錄

- [專案簡介](#專案簡介)
- [技術堆棧](#技術堆棧)
- [環境需求](#環境需求)
- [快速開始](#快速開始)
- [文件](#文件)
- [開發指南](#開發指南)

---

## 🎯 專案簡介

592Meal 是一個專為台灣街邊小吃攤商與企業駐點服務設計的訂餐平台，提供：

- ✅ **快速接單**：店家 < 5秒 完成接單
- ✅ **即時通知**：WebSocket 即時訂單通知
- ✅ **LINE 整合**：Login、Messaging、Pay 完整串接
- ✅ **駐點服務**：支援企業訂餐與現場發餐
- ✅ **離線模式**：無網路環境可查看訂單並記錄
- ✅ **多支付方式**：LINE Pay（店家獨立串接）+ 現金

---

## 🛠 技術堆棧

### 後端

- **Framework**: Laravel 12 (LTS)
- **前端框架**: Livewire 3
- **資料庫**: PostgreSQL 18
- **快取/佇列**: Redis 7.0
- **即時通訊**: Laravel Reverb (WebSocket)
- **管理後台**: Filament 3

### 前端

- **CSS Framework**: Tailwind CSS
- **JavaScript**: Alpine.js (Livewire 內建)
- **建置工具**: Vite

### 外部服務

- **LINE**: Login、Messaging API、Pay
- **Email**: Gmail SMTP
- **地圖**: Google Maps API

---

## 💻 環境需求

### 開發環境 (Laragon)

```
Windows 10/11
PHP 8.4+
Composer 2.7+
PostgreSQL 18+
Redis 7.0+
Node.js 20 LTS+
```

### 正式環境 (Docker)

```
Docker 24+
Docker Compose 2+
```

---

## 🚀 快速開始

### 方法一：使用初始化腳本（推薦）

```bash
# Windows
setup.bat

# Linux/Mac
chmod +x setup.sh
./setup.sh
```

### 方法二：手動安裝

#### 1. 複製環境變數檔案

```bash
copy .env.example .env  # Windows
# 或
cp .env.example .env    # Linux/Mac
```

#### 2. 安裝相依套件

```bash
# 安裝 PHP 套件
composer install

# 安裝 Node.js 套件
npm install
```

#### 3. 產生應用程式金鑰

```bash
php artisan key:generate
```

#### 4. 安裝 Laravel Reverb

```bash
php artisan reverb:install
```

#### 5. 設定資料庫

編輯 `.env` 填入資料庫資訊：

```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=oh592meal_dev
DB_USERNAME=postgres
DB_PASSWORD=你的密碼
```

建立資料庫：

```sql
CREATE DATABASE oh592meal_dev;
```

執行遷移：

```bash
php artisan migrate
```

#### 6. 啟動開發伺服器

```bash
# 使用 Laragon（推薦）
# 直接訪問 http://oh592meal.test

# 或使用 Artisan
php artisan serve
# 訪問 http://localhost:8000
```

#### 7. 啟動前端建置

```bash
# 開發模式（watch 檔案變更）
npm run dev

# 或建置一次
npm run build
```

#### 8. 啟動 WebSocket（可選）

```bash
# 另開終端機執行
php artisan reverb:start
```

---

## 🔐 測試帳號（開發環境）

### 後台管理登入

**後台網址：** `https://cms.oh592meal.test/admin`

| 角色 | 帳號 | 密碼 | 權限 |
|------|------|------|------|
| **Super Admin** | admin@592meal.com | `Admin@592meal2024` | 所有功能、所有店家 |
| **Store Owner** | luke2work@gmail.com | `password` | 僅自己的店家 (s000004, s000003) |

### 店家訂單管理

**Store Owner 可訪問的店家訂單管理：**
- 港式茶餐廳：`https://cms.oh592meal.test/store/s000004/manage/orders`
- 日式拉麵館：`https://cms.oh592meal.test/store/s000003/manage/orders`

**Super Admin** 可訪問所有店家的訂單管理。

### 快速測試流程

```bash
# 1. 登入後台
瀏覽器開啟: https://cms.oh592meal.test/admin
帳號: admin@592meal.com
密碼: Admin@592meal2024

# 2. 進入店家管理
點擊左側選單「店家管理」

# 3. 訪問訂單管理
點擊任意店家的「訂單管理」按鈕（橘色）
```

> 📝 **詳細測試帳號資訊請參考：** [`TEST_ACCOUNTS.md`](./TEST_ACCOUNTS.md)

---

## 📚 文件

### 環境配置

- [開發環境配置指南](docs/development-setup.md) - Laragon 設定、連線資料收集
- [環境配置檢查清單](docs/environment-checklist.md) - 填寫所有連線資料

### 設計文件

- [前端設計規格書](docs/front-end-spec.md) - 完整 UX/UI 設計
  - [UX Goals](docs/front-end-spec/01-ux-goals.md)
  - [Information Architecture](docs/front-end-spec/02-information-architecture.md)
  - [User Flows](docs/front-end-spec/03-user-flows.md)
  - [Wireframes](docs/front-end-spec/04-wireframes.md)
  - [Component Library](docs/front-end-spec/05-component-library.md)
  - [Branding & Style Guide](docs/front-end-spec/06-branding-style-guide.md)
  - [Accessibility](docs/front-end-spec/07-accessibility.md)
  - [Responsiveness](docs/front-end-spec/08-responsiveness.md)
  - [Performance](docs/front-end-spec/09-performance.md)
  - [Animations](docs/front-end-spec/10-animations.md)
  - [Next Steps](docs/front-end-spec/11-next-steps.md)
  - [Offline Mode](docs/front-end-spec/12-offline-mode.md)

### 產品文件

- [產品需求文件 (PRD)](docs/prd.md) - 完整產品規格
- [應用簡介](docs/app-brief.md) - 專案概述

### 測試與部署文件

- **[測試帳號與密碼](TEST_ACCOUNTS.md)** ⭐ - 完整測試帳號說明與密碼重設
- [後台認證整合報告](BACKEND_AUTH_INTEGRATION.md) - 訂單管理後台認證整合
- [訂單管理測試指引](TESTING_GUIDE.md) - 完整測試案例與故障排除
- [店家訂單管理說明](STORE_ORDER_MANAGEMENT.md) - 訂單管理功能使用手冊
- [店家訂單連結設定](STORE_ORDER_LINK.md) - 後台快速訪問連結說明

---

## 🔧 開發指南

### 常用指令

#### Laravel Artisan

```bash
# 清除快取
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# 資料庫
php artisan migrate
php artisan migrate:fresh
php artisan migrate:fresh --seed

# 建立檔案
php artisan make:model ModelName
php artisan make:controller ControllerName
php artisan make:livewire ComponentName

# 佇列
php artisan queue:work

# Reverb
php artisan reverb:start
```

#### Composer

```bash
# 安裝套件
composer install

# 新增套件
composer require vendor/package

# 自動載入優化
composer dump-autoload
```

#### NPM

```bash
# 開發模式
npm run dev

# 建置生產版本
npm run build
```

### 資料夾結構

```
oh592meal/
├── app/                    # Laravel 應用程式
│   ├── Http/
│   │   ├── Controllers/
│   │   └── Livewire/      # Livewire 組件
│   ├── Models/
│   ├── Services/          # 業務邏輯服務
│   └── Filament/          # Filament 管理後台
├── database/
│   ├── migrations/
│   └── seeders/
├── docs/                   # 專案文件
│   ├── front-end-spec/    # 前端設計規格
│   └── development-setup.md
├── docker/                 # Docker 配置
│   ├── nginx/
│   ├── php/
│   ├── postgres/
│   └── supervisor/
├── resources/
│   ├── views/             # Blade 模板
│   ├── css/
│   └── js/
├── routes/
│   ├── web.php
│   └── api.php
├── tests/
├── .env.example           # 環境變數範本
├── docker-compose.yml     # Docker Compose 配置
├── setup.bat              # Windows 初始化腳本
└── setup.sh               # Linux/Mac 初始化腳本
```

### 編碼規範

- **PSR-12** - PHP 編碼風格
- **Laravel Best Practices** - 遵循 Laravel 最佳實務
- **BEM** - CSS 命名規範
- **Atomic Design** - 組件設計模式

### 測試

```bash
# 執行所有測試
php artisan test

# 執行特定測試
php artisan test --filter=ExampleTest

# 生成測試覆蓋率報告
php artisan test --coverage
```

---

## 🐳 Docker 部署

### 建置並啟動容器

```bash
# 建置 Docker 映像
docker-compose build

# 啟動所有服務
docker-compose up -d

# 查看服務狀態
docker-compose ps

# 查看日誌
docker-compose logs -f

# 停止服務
docker-compose down
```

### 進入容器

```bash
# 進入 PHP 容器
docker-compose exec php sh

# 執行 Artisan 指令
docker-compose exec php php artisan migrate
```

---

## 📞 支援與聯絡

### 技術文件

- [Laravel 官方文件](https://laravel.com/docs)
- [Livewire 官方文件](https://livewire.laravel.com/docs)
- [Filament 官方文件](https://filamentphp.com/docs)
- [Tailwind CSS 文件](https://tailwindcss.com/docs)

### 外部服務文件

- [LINE Developers](https://developers.line.biz/en/docs/)
- [Google Maps API](https://developers.google.com/maps/documentation)

---

## 📄 授權

此專案為私有專案，未經授權不得使用。

---

## 🎉 開始開發

1. ✅ 確認環境需求已滿足
2. ✅ 執行初始化腳本 `setup.bat` 或 `setup.sh`
3. ✅ 填寫 `.env` 檔案（參考 `docs/environment-checklist.md`）
4. ✅ 執行資料庫遷移 `php artisan migrate`
5. ✅ 啟動開發伺服器與前端建置
6. ✅ 開始開發！

詳細配置說明請參考 [開發環境配置指南](docs/development-setup.md)

---

**最後更新**：2025-01-20
**維護者**：592Meal 開發團隊
