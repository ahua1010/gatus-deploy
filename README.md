# Gatus Deploy Guide

這個專案包含了部署 Gatus 監控系統的 Docker Compose 配置。

## 前置需求

- Docker
- Docker Compose
- Git

## 專案結構

  gatus-deploy/
  ├── docker-compose.yml # Docker 服務配置
  ├── config/
  │ └── config.yaml # Gatus 監控配置
  └── pgdata/ # PostgreSQL 數據持久化目錄


## Gatus 配置說明
### config.yaml 基本配置
https://gatus.io/docs



## 安裝步驟
### 1. 安裝 Docker 和 Docker Compose

  - 更新套件
      ```bash
      sudo apt-get update
      ```
  - 安裝必要套件
      ```bash
      sudo apt-get install ca-certificates curl gnupg lsb-release
      ```
  - 添加 Docker 的官方 GPG key
      ```bash
      sudo mkdir -p /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      ```
  - 設置 Docker repository
      ```bash
      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      ```
  - 安裝 Docker Engine 和 Docker Compose
      ```bash
      sudo apt-get update
      sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
      ```


### 2. 克隆專案
- 克隆專案
    ```bash
    git clone https://github.com/ahua1010/gatus-deploy.git
    cd gatus-deploy
    ```

### 3. 拉取映像
- 拉取 Gatus 自定義映像
    ```bash
    sudo docker pull liam1010/gatus-custom:latest
    ```
- 拉取 PostgreSQL 官方映像
    ```bash
    sudo docker pull postgres:latest
    ```

### 4. 啟動 Gatus
- 啟動 Gatus
    ```bash
    docker compose up -d
    ```


### 5. 驗證部署

- 訪問 `http://your-server-ip:8080` 查看 Gatus 監控介面。


### 6. 查看數據持久化

- Gatus 的監控數據會被持久化到 PostgreSQL 數據庫中。你可以通過以下方式查看：
    - 進入 PostgreSQL 容器
        ```bash
        sudo docker exec -it postgres psql -U gatus gatus
        ```
    - 查看所有表格
        ```bash
        \dt
        ```
    - 查看監控結果
        ```bash
        SELECT FROM endpoint_results ORDER BY created_at DESC LIMIT 5;
        ```

## 資料庫表格說明

- **endpoints**: 存儲監控端點的配置信息
- **endpoint_results**: 存儲監控檢查的結果
- **endpoint_result_conditions**: 存儲檢查條件的結果
- **endpoint_alerts_triggered**: 記錄觸發的警報
- **endpoint_events**: 記錄重要事件
- **endpoint_uptimes**: 記錄服務可用性統計

## 故障排除

如果遇到連接問題，請確認：

1. 防火牆是否開放 8080 端口：
    ```bash
    sudo ufw allow 8080/tcp
    ```
2. 確認 PostgreSQL 是否正常運行：
    ```bash
    sudo docker ps | grep postgres
    ```
