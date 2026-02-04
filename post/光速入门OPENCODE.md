---
author: Ryou
title: 光速入门 OPENCODE
date: 2026-02-04 21:04:57
lastmod: 2026-02-04 22:25:23
description:
draft: false
mermaid: true
image: https://w.wallhaven.cc/full/po/wallhaven-polllj.jpg
位置: 上海市 - 上海市
坐标:
  - 121.4768
  - 31.2243
温度: 10.6℃
风速: 5.1 m/s
天气: 多云
categories:
  - vibecode
tags:
  -
---


>[!summary] 前情提要
>作案动机：体验现代 VibeCOding
>- 资源：
>


## 前置条件
- 安装 bun
- 安装 [opencode](https://opencode.ai/)
- 科学上网
### 安装 BUN
- `powershell/cmd` 中执行
```powershell
powershell -c "irm bun.sh/install.ps1|iex"
```
### 安装
- 终端 执行
```bash
bun add -g opencode-ai
```
## 插件设置
- 使用别人的基础配置
### OHO 插件 安装
```cmd
bunx oh-my-opencode install
```
### 个人API 配置
```bash
// 输入 
opencode auth login
// 输出 例如 
┌  Add credential
│
◇  Select provider（直接选择 个人需要的 模型）
│  OpenCode Zen
│
●  Create an api key at https://opencode.ai/auth
│
◇  Enter your API key
│  ▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪▪
│
└  Done
```
#### Opencode
- 注册 配置 [key](https://opencode.ai/workspace/wrk_01KEB1GS72MSKV7ZP7SYP5JK56/keys)
#### github copilot （可选）
- `Select provider` 如果你有 就选择 `github copilot`
#### 个人 以 Nvidia 为例 -- 需要魔法
- [注册 Nvidia 个人开发者](https://cloudaccounts.nvidia.com/sf/v2/select-account?redirect_uri=https%3A%2F%2Flogin.nvidia.com%2Fcallback%2Fnca_picker&state=mjlv1qZEzxLzXt93-CgfsZiIEs4zLGYbezRiqmZ9heFMBbU7mKU31QfJBiovlCDam7yD9bQCvr3qS1bjUXqs-g&accounts=W10&count=0)  
- [minimax2.1](https://build.nvidia.com/minimaxai/minimax-m2) 
	- 点击`login` 
	- NOTE：要不然 你直接使用 google 账户 或其他第三方登录
	- 点击 [Generate Key](https://build.nvidia.com/settings/api-keys)创建 复制 Key 之后有用
- PS： 同理你也可以在之后使用 [其他免费 模型](https://build.nvidia.com/models)
##### 添加Custom Provide
- 终端输出 `opecode` 打开`opecode`  输出 `ctrl`+`p`
- ![image.png](https://cdn.jsdelivr.net/gh/wasidj/picBed@main/img/1770213423584-image.png)
- `enter` 选择 `nvidia`  输入之前的 注册的 API 
- 选择你喜欢的 开源模型。
## skill使用

| 网址                                                   | 功能              |
| ---------------------------------------------------- | --------------- |
| [Skills hub](https://www.skillhub.club/skills-guide) | skill 仓库（也推荐）   |
| [Skills MP](https://skillsmp.com/)                   | skill 仓库(我个人使用) |
- 直接使用 `bunx` 安装
- 例如
	- ![image.png](https://cdn.jsdelivr.net/gh/wasidj/picBed@main/img/1770213729450-image.png)

```bash
bunx skills add xx/xxx
```
## 推荐文章 
- [从 Prompt 到 Agent Skills：AI 在业务场景应用有了新玩法！（附私董会Case）](https://www.53ai.com/news/tishicikuangjia/2026012132879.html?utm_source=chatgpt.com)
- [AI 终于会"做事"了！Skills 如何让大模型从"知道"到"会做"](https://developer.volcengine.com/articles/7587309063479361546?utm_source=chatgpt.com)
- [Antigravity + Skills 快速打造页面 UI：从想法到上线完整指南](https://blog.wenhaofree.com/posts/articles/antigravity-skills-ui-design-guide/#:~:text=%E6%9C%AC%E6%96%87%E5%B0%86%E6%95%99%E4%BD%A0%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%20Antigravity%20%E5%92%8C%20Skills%20%E6%8A%80%E8%83%BD%E5%8C%85%EF%BC%8C%E9%80%9A%E8%BF%87%20AI%20%E8%BE%85%E5%8A%A9%E5%BF%AB%E9%80%9F%E6%9E%84%E5%BB%BA%E4%B8%93%E4%B8%9A%E3%80%81%E5%8F%AF%E7%94%A8%E3%80%81%E5%8F%AF%E5%A4%8D%E7%94%A8%E7%9A%84%E9%A1%B5%E9%9D%A2%E8%AE%BE%E8%AE%A1%E3%80%82%20%E6%97%A0%E8%AE%BA%E6%98%AF%E4%BA%A7%E5%93%81%E8%90%BD%E5%9C%B0%E9%A1%B5%E3%80%81%E8%90%A5%E9%94%80%E9%A1%B5%E9%9D%A2%E8%BF%98%E6%98%AF%E5%90%8E%E5%8F%B0%E7%95%8C%E9%9D%A2%EF%BC%8C%E9%83%BD%E8%83%BD%E6%89%BE%E5%88%B0%E9%AB%98%E6%95%88%E7%9A%84%E5%AE%9E%E7%8E%B0%E8%B7%AF%E5%BE%84%E3%80%82,%E5%92%8C%20Skills%EF%BC%9F%20Antigravity%20%E6%98%AF%E4%B8%80%E4%B8%AA%20AI%20%E9%A9%B1%E5%8A%A8%E7%9A%84%E9%A1%B5%E9%9D%A2%E7%94%9F%E6%88%90%E5%B7%A5%E5%85%B7%EF%BC%8C%E5%AE%83%E8%83%BD%E5%A4%9F%E7%90%86%E8%A7%A3%E8%87%AA%E7%84%B6%E8%AF%AD%E8%A8%80%E9%9C%80%E6%B1%82%E5%B9%B6%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90%E7%9B%B8%E5%BA%94%E7%9A%84%20UI%20%E4%BB%A3%E7%A0%81%E5%92%8C%E8%AE%BE%E8%AE%A1%E7%A8%BF%E3%80%82)
- [使用 pencil MCP 完成 UI/UX 设计](https://dotblogs.com.tw/yc421206/2026/01/31/pencil_ai_design_workflow_practical_guide)