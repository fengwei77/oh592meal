# 592Meal 雲端主機部署指南

## 前言

本指南說明如何將 592Meal 訂餐系統部署到雲端主機。系統採用 Laravel 12 + PHP 8.4 + PostgreSQL 18 技術棧，支援 Docker 容器化部署。

## GitHub 倉庫

- **主倉庫**: https://github.com/fengwei77/592meal
- **Laravel 專案**: 子模塊 `www`

## 部署步驟

### 1. 準備雲端主機環境

#### 系統需求
- Ubuntu 20.04+ 或 CentOS 8+
- Docker 20.10+
- Docker Compose 2.0+
- Git
- 至少 2GB RAM，建議 4GB+
- 至少 10GB 儲存空間

#### 安裝 Docker (Ubuntu)
```bash
# 更新系統
sudo apt update && sudo apt upgrade -y

# 安裝 Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 安裝 Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 將使用者加入 docker 群組
sudo usermod -aG docker $USER
newgrp docker
```

### 2. 克隆專案

```bash
# 克隆主倉庫
git clone https://github.com/fengwei77/592meal.git
cd 592meal

# 初始化並更新子模塊
git submodule update --init --recursive
```

### 3. 環境設定

#### 建立 .env 檔案
```bash
# 複製環境範本
cp www/.env.example www/.env

# 編輯環境設定
nano www/.env
```

#### 環境變數配置
```env
# 應用程式設定
APP_NAME="592Meal"
APP_ENV=production
APP_KEY=base64:YOUR_APP_KEY_HERE
APP_DEBUG=false
APP_URL=https://your-domain.com

# 資料庫設定
DB_CONNECTION=pgsql
DB_HOST=postgres
DB_PORT=5432
DB_DATABASE=oh592meal_prod
DB_USERNAME=oh592meal
DB_PASSWORD=your_secure_password

# Redis 設定
REDIS_HOST=redis
REDIS_PASSWORD=your_redis_password
REDIS_PORT=6379

# 檔案上傳設定
FILESYSTEM_DISK=public
MEDIA_DISK=public

# LINE Login 設定
LINE_CHANNEL_ID=your_line_channel_id
LINE_CHANNEL_SECRET=your_line_channel_secret
LINE_LOGIN_CALLBACK_URL=https://your-domain.com/auth/line/callback

# 推播通知設定
VAPID_PUBLIC_KEY=your_vapid_public_key
VAPID_PRIVATE_KEY=your_vapid_private_key
VAPID_SUBJECT=mailto:admin@your-domain.com
```

### 4. 產生應用程式金鑰

```bash
# 進入 Laravel 目錄
cd www

# 安裝 Composer 依賴
composer install --optimize-autoloader --no-dev

# 產生應用程式金鑰
php artisan key:generate

# 安裝 NPM 依賴並編譯前端資源
npm install
npm run build
```

### 5. 啟動 Docker 服務

```bash
# 回到專案根目錄
cd ..

# 啟動所有服務
docker-compose up -d

# 查看服務狀態
docker-compose ps
```

### 6. 資料庫初始化

```bash
# 進入 PHP 容器
docker-compose exec php bash

# 執行資料庫遷移
php artisan migrate --force

# 執行資料庫填充
php artisan db:seed --force

# 建立儲存連結
php artisan storage:link

# 設定檔案權限
php artisan storage:permission

# 清除快取
php artisan config:clear
php artisan cache:clear
php artisan view:clear

# 離開容器
exit
```

### 7. 設定 SSL 憑證 (可選)

#### 使用 Let's Encrypt
```bash
# 安裝 Certbot
sudo apt install certbot python3-certbot-nginx

# 取得 SSL 憑證
sudo certbot --nginx -d your-domain.com -d cms.your-domain.com

# 自動續期
sudo crontab -e
# 添加以下行：
# 0 12 * * * /usr/bin/certbot renew --quiet
```

### 8. 設定 Cron Job

```bash
# 編輯 crontab
crontab -e

# 添加 Laravel 排程任務
* * * * * cd /path/to/592meal/www && php artisan schedule:run >> /dev/null 2>&1
```

### 9. 檢查部署狀態

```bash
# 檢查服務運行狀態
docker-compose ps

# 查看服務日誌
docker-compose logs -f

# 測試網站是否正常運作
curl -I http://localhost
```

## 網域設定

### DNS 記錄
```
A    @        YOUR_SERVER_IP
A    cms      YOUR_SERVER_IP
```

### Nginx 配置範例
如果需要自訂 Nginx 配置，可以修改 `docker/nginx/conf.d/default.conf`：

```nginx
# 前台網站
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your-domain.com www.your-domain.com;

    ssl_certificate /etc/nginx/ssl/your-domain.crt;
    ssl_certificate_key /etc/nginx/ssl/your-domain.key;

    root /var/www/html/public;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}

# 後台管理
server {
    listen 80;
    server_name cms.your-domain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name cms.your-domain.com;

    ssl_certificate /etc/nginx/ssl/your-domain.crt;
    ssl_certificate_key /etc/nginx/ssl/your-domain.key;

    root /var/www/html/public;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

## 維護指令

### 日常維護
```bash
# 查看服務狀態
docker-compose ps

# 查看即時日誌
docker-compose logs -f

# 重新啟動特定服務
docker-compose restart php

# 更新程式碼
git pull origin master
git submodule update --remote
docker-compose exec php composer install --optimize-autoloader --no-dev
docker-compose exec php php artisan migrate
docker-compose exec php php artisan config:clear
docker-compose exec php php artisan cache:clear
```

### 備份
```bash
# 備份資料庫
docker-compose exec postgres pg_dump -U oh592meal oh592meal_prod > backup_$(date +%Y%m%d_%H%M%S).sql

# 備份檔案
tar -czf storage_backup_$(date +%Y%m%d_%H%M%S).tar.gz www/storage/app/public/
```

### 復原
```bash
# 恢復資料庫
docker-compose exec -T postgres psql -U oh592meal oh592meal_prod < backup_20231201_120000.sql

# 恢復檔案
tar -xzf storage_backup_20231201_120000.tar.gz
```

## 故障排除

### 常見問題

#### 1. 502 Bad Gateway
```bash
# 檢查 PHP-FPM 是否運行
docker-compose exec php ps aux | grep php-fpm

# 重新啟動 PHP 服務
docker-compose restart php
```

#### 2. 資料庫連線失敗
```bash
# 檢查 PostgreSQL 服務
docker-compose exec postgres pg_isready -U oh592meal

# 檢查網路連線
docker-compose exec php ping postgres
```

#### 3. 檔案權限問題
```bash
# 修復儲存目錄權限
docker-compose exec --user root php chown -R www:www /var/www/html/www/storage
docker-compose exec --user root php chmod -R 755 /var/www/html/www/storage
```

#### 4. SSL 憑證問題
```bash
# 檢查憑證有效期
openssl x509 -in /etc/nginx/ssl/your-domain.crt -text -noout | grep "Not After"

# 重新載入 Nginx 配置
docker-compose exec nginx nginx -s reload
```

## 效能優化

### PHP 設定優化
編輯 `docker/php/php.ini`：
```ini
memory_limit = 512M
upload_max_filesize = 20M
post_max_size = 20M
max_execution_time = 300
opcache.enable=1
opcache.memory_consumption=128
```

### Nginx 設定優化
編輯 `docker/nginx/conf.d/default.conf`：
```nginx
worker_processes auto;
worker_connections 1024;

gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

## 安全建議

1. **定期更新**：定期更新 Docker 映像和系統套件
2. **防火牆設定**：只開放必要的端口 (80, 443, 22)
3. **監控日誌**：定期檢查應用程式和系統日誌
4. **備份策略**：建立自動化備份機制
5. **SSL 憑證**：確保使用 HTTPS 且憑證有效
6. **資料庫安全**：使用強密碼，限制資料庫存取

## 聯絡資訊

如有部署問題，請參考：
- GitHub Issues: https://github.com/fengwei77/592meal/issues
- 技術文件：參考專案 README.md

---

**部署完成後系統將可透過以下網址存取：**
- 前台網站: `https://your-domain.com`
- 後台管理: `https://cms.your-domain.com`
- 訂單管理: `https://cms.your-domain.com/store/{store_slug}/manage/orders`