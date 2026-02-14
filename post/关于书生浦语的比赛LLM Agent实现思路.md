---
author: Ryou
title: 关于书生浦语的比赛LLM Agent实现思路
date: 2026-02-13T16:10:02+08:00
lastmod: 
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/qr/wallhaven-qrjmgl.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 19.7℃ 
风速: 4.1 m/s
天气: 多云
categories:
  - 
tags:
  - 
---


>[!summary] 前情提要
>- 书生浦语: 比赛有奖金 任务比较明确 （评分机制也被开源出来了）
>- 任务比较明确，想在开发机中配置 opencode 使用 vibecoding 自动迭代训练，获取奖金
>- 关键的效果提升评估：
>	- [ ]  只能使用 **/share/new_models/InternVL3.5/InternVL3.5-1B**

## 1. dae 环境配置
## 2. 配置 OpenCode  && OHO
## 3. 自定义 比赛 SKILLS(可以直接使用create agent 来创建)
## 4. 一直迭代 LOOP （开发机 算力太贵）PS:本地定时脚本

- 尝试优化的 方向：
	1. 数据优化
	2. System prompt 优化
	3. 训练参数优化

### 现在的大致思路
- [ ] `SSHFS` 或者 直接 copy 数据集 本地数据强化 再上传到  SSH 计算训练
