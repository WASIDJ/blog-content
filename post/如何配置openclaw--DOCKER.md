---
author: Ryou
title: OpenClaw 安装部署与飞书接入完全指南
date: 2026-03-07T22:00:00+08:00
lastmod: 
description: 详解在 Arch Linux 上安装 OpenClaw 并接入飞书机器人的完整流程
draft: false
mermaid: true
image: https://w.wallhaven.cc/full/d8/wallhaven-d8kgz3.jpg
位置: Shanghai - Shanghai
坐标: 
  - 121.4581
  - 31.2222
温度: 5.3℃ 
风速: 5.6 m/s
天气: 阴天
categories:
  - AI
  - OpenClaw
tags:
  - OpenClaw
  - AI Agent
  - 飞书
  - 自动化
---
## 快速开始

```bash
# 拉取镜像
docker pull alpine/openclaw:main

# 或者使用官方镜像（推荐）
docker pull ghcr.io/openclaw/openclaw:latest
```

## 完整部署指南

### 方式一：使用官方脚本（推荐）

官方提供了自动化的 Docker 部署脚本：

```bash
# 克隆官方仓库
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# 运行自动安装脚本
./docker-setup.sh
```

这个脚本会：
- 构建或拉取 Docker 镜像
- 运行配置向导
- 启动 Gateway
- 生成并写入 token 到 `.env`

### 方式二：使用 Docker Compose

创建 `docker-compose.yml`：

```yaml
services:
  openclaw:
    image: ghcr.io/openclaw/openclaw:latest
    container_name: openclaw
    restart: unless-stopped
    working_dir: /app
    environment:
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - HOME=/app
    volumes:
      - openclaw-config:/app/.openclaw
    ports:
      - "18789:18789"

volumes:
  openclaw-config:
```

创建 `.env` 文件：

```bash
ANTHROPIC_API_KEY=sk-ant-your-key-here
OPENAI_API_KEY=sk-your-key-here
```

启动服务：

```bash
docker compose up -d
docker compose logs -f openclaw
```

## 镜像说明

| 镜像地址 | 说明 |
|---------|------|
| `alpine/openclaw:main` | Docker Hub 镜像（自动从官方同步） |
| `ghcr.io/openclaw/openclaw:latest` | GitHub 官方镜像（推荐） |
| `ghcr.io/openclaw/openclaw:<version>` | 指定版本，如 `2026.2.26` |

> ⚠️ 注意：虽然镜像名为 `alpine/openclaw`，但实际基于 Debian Bookworm 构建（因 musl 兼容性问题）

## 访问与配置

1. 打开浏览器访问：`http://127.0.0.1:18789/`
2. 在设置中粘贴 token 进行配对
3. 获取新的 Dashboard 链接：
   ```bash
   docker compose run --rm openclaw-cli dashboard --no-open
   ```

## 常用命令

```bash
# 启动
docker compose up -d

# 查看日志
docker compose logs -f openclaw

# 停止
docker compose down

# 重启
docker compose restart openclaw

# 健康检查
curl -fsS http://127.0.0.1:18789/healthz
```

## 集成飞书/Telegram/Discord

**Telegram：**
```bash
docker compose run --rm openclaw-cli channels add --channel telegram --token "<token>"
```

**Discord：**
```bash
docker compose run --rm openclaw-cli channels add --channel discord --token "<token>"
```

**WhatsApp（QR 登录）：**
```bash
docker compose run --rm openclaw-cli channels login
```

## 配置选项

### 使用预构建镜像跳过本地构建

```bash
export OPENCLAW_IMAGE="ghcr.io/openclaw/openclaw:latest"
./docker-setup.sh
```

### 持久化配置目录

```bash
export OPENCLAW_HOME_VOLUME="openclaw_home"
./docker-setup.sh
```

### 安装额外系统包

```bash
export OPENCLAW_DOCKER_APT_PACKAGES="ffmpeg build-essential"
./docker-setup.sh
```

## 权限问题

容器以 `node` 用户（uid 1000）运行。如遇权限错误：

```bash
sudo chown -R 1000:1000 /path/to/openclaw-config
```

## 资源要求

- 最低 2 GB RAM
- Docker 20.10+
- 足够的磁盘空间用于镜像和日志