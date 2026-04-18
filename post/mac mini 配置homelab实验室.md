---
author: Ryou
title: mac mini 配置homelab实验室
date: 2026-04-18T15:08:36+08:00
lastmod: 
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/po/wallhaven-pojl63.png
位置: Shanghai - Shanghai
坐标: 
  - 121.4581
  - 31.2222
温度: 19.5℃ 
风速: 8.8 m/s
天气: 阴天
categories:
  - 
tags:
  - 
---
[- 步骤：
	1. [ ]  macmini 服务器模式
	2. [ ] CF 内网穿透方案
- 我想把笔记本中 1TB的长江存储格式化 给mac mini用
	- 装到 orico的 VSE中 连接 mac mini 让它自己格式化 APFS
- 参考教程：
	- [Mac mini不买显示器行不行？我测试了一下](https://www.bilibili.com/video/BV1ggfbBLE5d/?vd_source=4b0ee375ec8ee9fb646ccb2361a5ccb4)
### 前置步骤：IPAD 显示 Mac mini
- IPAD 安装 yam display
- mac mini 安装 yam display 
	- 权限
### 前置步骤：无显示器启动  

### CF 内网穿透](<按你给的结构补齐，并做成**可执行的工程笔记版本**（去掉冗余，强调步骤与依赖关系）。

---

# Mac mini Homelab 初始化方案（Headless + CF Tunnel）

---

## 步骤

1. [ ] mac mini 服务器模式
2. [ ] Cloudflare 内网穿透方案

---

## 存储准备（1TB 长江存储 → mac mini）

### 目标

把 NVMe SSD 作为 mac mini 的外置数据盘（APFS）

### 硬件

* ORICO VSE（M.2 扩展坞）
* NVMe SSD（1TB）

### 连接

```text
SSD → ORICO VSE → USB-C → mac mini
                 ↑
               5V供电（必须接）
```

---

### 格式化（在 mac mini 上执行）

在 macOS：

#### GUI

1. 打开「磁盘工具」
2. 显示所有设备
3. 选择整块 SSD
4. 抹掉：

```text
格式：APFS
方案：GUID 分区图
名称：homelab
```

---

#### CLI（推荐）

```bash
diskutil list
diskutil eraseDisk APFS homelab GPT /dev/diskX
```

---

### 挂载建议（为后续服务准备）

```bash
mkdir -p ~/data
```

建议结构：

```text
~/data/
  ├── docker/
  ├── models/
  ├── db/
  └── logs/
```

---

## 前置步骤：iPad 显示 mac mini（临时显示方案）

![Image](https://images.openai.com/static-rsc-4/BDVEIkSVGKy5XEgRj-tj7gq_TghrzL4oIYx235jSNpzpMa_ON66O_Wb66MZ9-f9ILYXxsE1Iw2JjZCXgVQOEhOmceTZTxQPKwzIuHaTkMWnKmJ3FS2ZTofX4eqRxpZvqa68OL-ydNB1t1IZqF830sJo1dcLpGJr4Ndv2JgzOUbi925iyNIXgI2zT-ylNYotu?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/5Fv-nLTTHWovP8f7YMZOixfUqnSrwpQ8M2opATkJRHxM6wJJ7SZz-qZNvMqlxRS8ju8udLMbF6fd1ofOQ5wvYs9CFzm0ZEsa0bfvthdlCtEM9jctlGncK-_8OW88Kip90ZxgS73YLq9GktdV4j75kY_9uDZDtaDJZej72iNoRWJ5IxoL4BB3OpBZ1LT_fkvW?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/GWHnT_R2GXI_AOg6ryCyrapsBfsRh7Bj3fZ4Akecc9ylS6RNYeW3_Iq_8l_3uMXIURN_EY3q2rrn7iu8jGA5Ks7RganmIQ4H3Y1LSCMk0ZsbBi-g8BOBlVfgPUHgNzzpewtbn3F0slVuZo0NTOt4dYc7sd_GyKpO4pB5gLTduZ54C5WNn0DAKKme--NZ1Pjj?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/8jy-Abf-XCZwXFTSAXnG3GeTP_-Wpqe_xLEU83xW5TjDIqDQCCCipOGB7QxgIKgtj96GSpt6BQzJaRAH-fwxQw-lBjsJ64mhQ-K6efaJyWgI2FdBIxjexEtQKX0__SG2qdgMZd3LvWhGomK3LSEFxOEwW5goxnZ8dALOqAcA3luCG2NmFyr7u8OfR1KxqKNU?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/oSwJXicZWiKhcflo3G9tpBQ8W2haaiVHHe9hYeb4t25xSaUAPjxHq77fVnbSXpn2E36hTTRNh0qTeGuUs_sjxR-FrJvA-V7pBbgPUz0FZg6jwW8l4Yap0aQtOr6Rx5u8E2dvGW0FxJOXpPyUUKT4aXtOJlqaGX-X1hPlCyX3P0YCpjs1w1A31Lq9rePvywwv?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/8jy-Abf-XCZwXFTSAXnG3GeTP_-Wpqe_xLEU83xW5TjDIqDQCCCipOGB7QxgIKgtj96GSpt6BQzJaRAH-fwxQw-lBjsJ64mhQ-K6efaJyWgI2FdBIxjexEtQKX0__SG2qdgMZd3LvWhGomK3LSEFxOEwW5goxnZ8dALOqAcA3luCG2NmFyr7u8OfR1KxqKNU?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/7h_XqKPG6FIYkbvNFNGAWgTSsJIWPS80UQNgdZwGF8_iXNg603yLIYKfb_FRH55khpXZL36jwhb-lX_16iwj7xAH9MDIFEwBAHjk8xxEufxQC5ucTkw8EyKh7PBKkYgYzzDr_1wxdO6g_jm98lBbV6yXmuTuSixk3bYEsJ-MfJAfS0tdNEWknYufjJ4n28uz?purpose=fullsize)

### 目标

在没有显示器情况下完成初始配置

---

### 操作

1. iPad 安装 Yam Display
2. mac mini 安装 Yam Display
3. 连接（USB 或 Wi-Fi）

---

### 权限（mac mini）

在 macOS：

* 屏幕录制
* 辅助功能
* 输入监控

---

### 说明

* 仅用于**首次配置**
* 不建议长期依赖（延迟 + 不稳定）

---

## 前置步骤：无显示器启动（Headless）

### 目标

让 mac mini 无显示器也可远程控制

---

### Step 1：开启远程能力

系统设置 → 通用 → 共享：

* [x] 远程登录（SSH）
* [x] 屏幕共享（VNC）

---

### Step 2：获取 IP

```bash
ipconfig getifaddr en0
```

---

### Step 3：测试 SSH

在笔记本：

```bash
ssh user@192.168.x.x
```

---

### Step 4：禁用睡眠（关键）

```bash
sudo pmset -a sleep 0 disksleep 0 displaysleep 0
```

---

### Step 5：可选（假显示器）

![Image](https://images.openai.com/static-rsc-4/l0NmYishE4Icsq9iDVOz0XHDD0PSwPXQ7Yc5kufo1GbyrGnp8cMqtHrNH8LyXbSLOOz79BaBCccIY8Re0WXb8kNHv0qIaydhs88Xz_ma5vEhhgM1P10zi-QgYF995uRHgt1oHO_TX0nE5aEKFw1nVzor6OIUys4EEUKvdo3_jQhLElXC1LI8otynVLMZ-xOk?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/RQEyVxPjIeJG0C571M3khWp5VcK2u5uKIaOA4AQweFvMg3sjUPTntLDWbzwqw2zpt2fEpVJDIKVFnB4b-2sBO7OIbEZysaB848zLLMWDeYRVgsvvsS_JK247rnHa_MT5QllRBxEDXGGpZfeI0gZhaNjHIiCAkOgX0Af3QqFJXvaPoktwgwxulyjgKAybjuJ3?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/gnkr_65M6VhnRvjGznkz4pBELWabHXUCsnaKNfVowONtyZ5JnPNMY6ffp4GDHP1UjPPC4_-bpl7hjzVXXlpvXZ6z6em-99Og9MyccYsXu-SujPhl_pP0UnO-eID26dhSSNP7vU5HccUT42RG7xlwlNqMyq6GoQdcoXagUROiUb2S0lTmhoUIb5ftq2oIvkba?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/i_pce6EbQyUkbJWpvXni3mmYrFwOzklXXJYaVATSd6k_32JBaxTiSu3wv5djOjLgTHzKcv2BoxB8yja_9p9_4IjmBWXRe_u0thskKfyb6yVYfdaht-FyRCkzyj32OadfWMogxNvLbhITdZeWkb01WAPfxCayRs6jJ3yF5ClPrqxozgz8CzxdPgXKQnaJdjD1?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/Vvsv68KjjnIU2bgeYGmdeEwFbm0FjKuX7O1vcKqPtv03mDdW3Pb8zTEzL3rMFV51__DPPXTYNxW6_lJ7s_XelSfcguZWFrHp5rztV2bUNGgKNBxvkdNGxvwZfWV0P71t8Z2vO1cpfgMKdieIKjS-pSSmUkeI1Vqtkz-dTkrR8cp170qRee92MXqZ748BewCM?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/2Qt8H-B9trC1H8ofwboHGFzT_M-TAp8pEgTugJ-uVlDi8KinLZoOGFacqjx3SzX2CYEhueoC0KGleUFAa6rwbODFtbbf8nv1kvJ6TPswKCxM1S2TQLzxIbgxg5ENE0KyWbCb1nznB3H7nReyvtLIFC54tX3ZA--4mW3Vn6teGnAyURWecSCTnFlXr-FfQwJJ?purpose=fullsize)

* HDMI Dummy Plug
* 作用：

  * 保持分辨率
  * 避免远程桌面黑屏

---

## mac mini 服务器模式

### 目标

稳定运行 + 可远程管理

---

### 基础配置

```bash
# 禁用睡眠
sudo pmset -a sleep 0

# 开启 SSH（已在 GUI 做）
sudo systemsetup -setremotelogin on
```

---

### 环境准备

```bash
# 安装 brew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 基础工具
brew install git docker docker-compose cloudflared
```

---

### Docker 初始化

```bash
mkdir -p ~/docker
cd ~/docker
```

---

## 网络：固定 IP（DHCP 绑定）

### 目标

避免 IP 变化导致 SSH / 服务失效

---

### 步骤

1. 获取 MAC 地址：

```bash
ifconfig en0 | grep ether
```

2. 登录路由器（如 192.168.1.1）

3. 添加绑定：

```text
MAC → 固定 IP（如 192.168.1.10）
```

---

## Cloudflare 内网穿透

### 目标

从外网访问 homelab 服务

---

### 注册与准备

* 注册 Cloudflare
* 添加域名

---

### 安装

```bash
brew install cloudflared
```

---

### 登录

```bash
cloudflared tunnel login
```

---

### 创建 Tunnel

```bash
cloudflared tunnel create homelab
```

---

### 配置

```yaml
tunnel: homelab
credentials-file: /Users/ryou/.cloudflared/xxxx.json

ingress:
  - hostname: ssh.yourdomain.com
    service: ssh://localhost:22

  - hostname: app.yourdomain.com
    service: http://localhost:3000

  - service: http_status:404
```

---

### 运行

```bash
cloudflared tunnel run homelab
```

---

### 开机自启（重要）

```bash
brew services start cloudflared
```

---

## 验证

### SSH（外网）

```bash
ssh user@ssh.yourdomain.com
```

---

### Web 服务

```text
https://app.yourdomain.com
```

---

## 最终结构

```text
mac mini（控制节点）
  ├── APFS SSD（ORICO）
  ├── Docker 服务
  ├── Cloudflare Tunnel
  └── SSH 控制

笔记本
  ├── SSH 客户端
  └── 开发环境
```

---

## 关键结论

* APFS 必须在 macOS 格式化
* ORICO 的 5V 必须接（否则 SSD 不稳定）
* Headless 依赖 SSH，不依赖显示器
* Cloudflare Tunnel 替代公网 IP

---