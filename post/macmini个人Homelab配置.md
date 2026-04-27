---
author: Ryou
title: macmini个人工作站配置
date: 2026-04-25T21:02:13+08:00
lastmod:
description:
draft: false
mermaid: true
image: https://w.wallhaven.cc/full/ml/wallhaven-mlwz78.png
位置: Shanghai - Shanghai
坐标:
  - 121.4581
  - 31.2222
温度: 14.2℃
风速: 6.9 m/s
天气: 晴朗
categories:
  - vibecode
  - homelab 
tags:
  - codex
  - OpenCode
  - Claude
---
- 作案动机：
	- 过年期间 VLM模型微调比赛 对数据进行增强的时候，发现 `LLM Agent` 真的好用。
	- 学院分配数据处理工作，去闲鱼质询了相似的价格 要 1K。
	- 比较主流的 [MLEvolve](https://github.com/InternScience/MLEvolve) 对数据分析，效果挺好。
	- 受到 **《第四公民》（Citizenfour）**[^1] 影响  `纪录片的结尾 特别好：过去我们称之为 自由的东西，现在我们称之为隐私。当我们失去隐私，我们失去媒介，我们也就失去自由本身，因为我们再也无法自由地表达自己的看法。最终我们也会失去自我。`
	- 纳瓦尔宝典[^2] 需要一个 24H 为你工作的 机器
- Macmini优劣：
	- 优势：
		- 保值率高（内存和存储双双暴涨）macmini绝对是性价比之选
			- 芯片制程 我一个外行的使用体验：感觉已经到头了。之后macmini的发展将会浮现 发展一百多年的汽车市场
		- 安静 节能
		- 类Unix系统
		- 安全：以后的供应链攻击会非常普遍，到现在为止 出现了 apifox axios litellm。macmini 是 FIDO2的另一种选择
	- 劣势：
		- 不够自由
		- 被生态绑架，用着用着就像买苹果全家桶
			- 但Ipad OS 是真的难用，比如 `termux` 不支持 ；`termuis`对 `tmux status bar` 支持不好。
- 大致步骤：
	1. [x] 购买Macmini（直接买二手的）
	2. [x] 开机设置无头启动
	3. [x] 设置固定的IP
	4. [x] 配置基础环境
	5. [x] 定制化`.config`
	6. [x] 长期使用

### 2. 开机设置无头启动
#### 关闭`FileVault`
en：`Apple menu → System Settings → Privacy & Security → FileVault`
ch：`Apple 菜单 → 系统设置 → 隐私与安全性 → FileVault`
- 点击关闭，输入密码就可以了
终端输入也行：
```bash
sudo fdesetup disable
# 检查
fdesetup status
```
#### 设置来电自动开机（断电恢复）与 禁止睡眠（服务器模式）
```bash
# 断电恢复
sudo pmset -a autorestart 1
pmset -g | grep autorestart
# 应该显示
> autorestart 1
# 禁止睡眠
sudo pmset -a sleep 0
sudo pmset -a displaysleep 30
sudo pmset -a disksleep 0
sudo pmset -a tcpkeepalive 1
sudo pmset -a womp 1
# 开启ssh 
# 打开 macOS 的 Remote Login（远程登录）功能
sudo systemsetup -setremotelogin on
```

## 3. 设置固定的IP(wifi，长期网线不需要设置)
```bash
# 网络服务名
> networksetup -listallnetworkservices
An asterisk (*) denotes that a network service is disabled.
Ethernet
Thunderbolt Bridge
Wi-Fi
# 你的 ip 
>ipconfig 
getifaddr en1 192.168.1.78
# WIFI DHCP with manual address 
>sudo networksetup -setmanualwithdhcprouter "Wi-Fi" 192.168.1.78
# 网线
>networksetup -setmanualwithdhcprouter "Ethernet" 192.168.1.78
```
## 4. 配置基础环境
### 包管理器
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
### 核心环境
```bash
brew install tmux git gh neovim wget curl fzf ripgrep fd jq htop btop yazi lazygit bun tmux
```
### 远程访问
```bash
> brew install --cask tailscale
# 然后启动 首次启动 需要登录
> tailscale up
# 查看状态
> tailscale status
# 查看本机IP
> tailscale ip
# 创建deamon
> sudo brew services start tailscale
```

### AI coding 层
```bash
bun install -g @openai/codex @anthropic-ai/claude-code
brew install anomalyco/tap/opencode
```

## 5. 定制化`.config`
- 进入到 `~/.config`中自己找要配置的项目
- 安装字体：`brew install --cask font-jetbrains-mono-nerd-font`
### `tmux`config
- 不会用，让LLM告诉你。
- 配置 TPM
```bash
git clone https://github.com/tmux-plugins/tpm ~/.config/tmux/plugins/tpm
```
- `~/.config/tmux/tmux.config` 我的配置：
```config
# ============================================
# 基础设置
# ============================================
set-option -g default-shell /bin/zsh
set-environment -g PATH "/Users/ryou/.bun/bin:/Users/ryou/.local/bin:/opt/homebrew/bin:/opt/homebrew/sbin:/home/ryou/.local/bin:/home/ryou/.npm-global/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
set -g mouse on
set -g mode-keys vi
set -g clock-mode-style 24
set -g history-limit 100000
set -s extended-keys on
bind-key -n S-Enter send-keys -H 1b 5b 31 33 3b 32 75
set -g @catppuccin_window_text " #W"
set -g @catppuccin_window_current_text " #W"
set -g allow-passthrough on
# 前缀键
unbind C-b
set -g prefix C-q
bind C-q send-prefix

# ============================================
# Session 管理
# ============================================
# 创建新 session
bind C-c new-session
# ================================
# Session 列表、删除管理菜单
# ================================
# 弹出菜单（上下选择，Enter 切换，x 删除）
bind u choose-session
# Session 列表 (交互式输入选择)
# 使用 g 避免与 pane 分屏键 s/S 产生操作混淆
bind g command-prompt -p "switch to session:" "switch-client -t '%%'"
# 下一个 session
bind ) switch-client -n
# 上一个 session
bind ( switch-client -p
# 重命名 session
bind C-r command-prompt -p "rename session:" "rename-session '%%'"
# 快速切换到上一个活跃 session
bind b switch-client -l
# 关闭当前 session (不带确认)
bind Q kill-session

# ============================================
# Window 管理
# ============================================
# 新建 window (保持当前路径)
bind c new-window -c "#{pane_current_path}"
# Window 导航 (直接跳转)
bind 1 select-window -t :1
bind 2 select-window -t :2
bind 3 select-window -t :3
bind 4 select-window -t :4
bind 5 select-window -t :5
bind 6 select-window -t :6
bind 7 select-window -t :7
bind 8 select-window -t :8
bind 9 select-window -t :9

# 上/下一个 window
bind -n M-b previous-window
bind -n M-n next-window

# 重命名 window（用 prefix + i 独立快捷键，避免被占用）
bind r command-prompt -p "rename window: " "rename-window '%%'"
bind , command-prompt -p "rename window: " "rename-window '%%'"
# 关闭当前 window (带确认)
bind X confirm-before -p "kill window #W? (y/n)" kill-window

# ============================================
# Pane 管理 (保留你的设置)
# ============================================
# 分屏 (mnemonic: v 竖/s 横)
bind v split-window -h -c "#{pane_current_path}"
bind s split-window -v -c "#{pane_current_path}"
# Alt + hjkl 切换 pane
bind -n M-h select-pane -L
bind -n M-j select-pane -D
bind -n M-k select-pane -U
bind -n M-l select-pane -R
# Alt + Shift + hjkl 调整 pane 大小 (支持重复按键)
bind -nr M-H resize-pane -L 5
bind -nr M-J resize-pane -D 5
bind -nr M-K resize-pane -U 5
bind -nr M-L resize-pane -R 5
# 关闭当前 pane (带确认)
bind x kill-pane
# 最大化/还原 pane (zoom)
bind z resize-pane -Z

# ============================================
# 复制模式
# ============================================
# 进入复制模式
bind Enter copy-mode
# v 开始选择, y 复制
bind -T copy-mode-vi v send-keys -X begin-selection
bind -T copy-mode-vi y send-keys -X copy-selection-and-cancel
# ============================================
# 插件
# ============================================
# 先加载 catppuccin 主题（确保窗口名显示正确）
run '~/.config/tmux/plugins/catppuccin/catppuccin.tmux'

set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'        # 会话持久化
set -g @plugin 'tmux-plugins/tmux-continuum'         # 自动保存/恢复
set -g @plugin 'nhdaly/tmux-better-mouse-mode'       # 更好的鼠标支持
set -g @plugin 'catppuccin/tmux'

# resurrect 配置
set -g @resurrect-capture-pane-contents 'on'
set -g @continuum-restore 'on'

# Catppuccin status modules
set -g status-left-length 100
set -g status-right-length 220
set -g status-left "#[fg=#{?client_prefix,#{E:@thm_red},#{E:@thm_green}}]#[fg=#{@thm_crust},bg=#{?client_prefix,#{E:@thm_red},#{E:@thm_green}}] #[fg=#{@thm_fg},bg=#{@thm_surface_0}] #S #[fg=#{@thm_surface_0},bg=default] "
set -g status-right "#[fg=#{@thm_pink},bg=default]#[fg=#{@thm_crust},bg=#{@thm_pink}] #[fg=#{@thm_fg},bg=#{@thm_surface_0}] #{pane_current_command} #[fg=#{@thm_mauve},bg=#{@thm_surface_0}]#[fg=#{@thm_crust},bg=#{@thm_mauve},bold] Codex #[fg=#{@thm_fg},bg=#{@thm_surface_0}] #[fg=#{@thm_lavender},bg=#{@thm_surface_0}]#[fg=#{@thm_crust},bg=#{@thm_lavender}]󰒋 #[fg=#{@thm_fg},bg=#{@thm_surface_0}] #H #[fg=#{@thm_sky},bg=#{@thm_surface_0}]#[fg=#{@thm_crust},bg=#{@thm_sky}]󰃰 #[fg=#{@thm_fg},bg=#{@thm_surface_0}] %H:%M #[fg=#{@thm_surface_0},bg=default]"
# End Catppuccin status modules

run '~/.config/tmux/plugins/tpm/tpm'
```
- 然后进入`tmux` `Ctrl-q 然后 Shift+i` 安装插件

## 长期使用
- 其他 移动设备（同样需要登录 `tailscale`）
- 设置 `~/.ssh/config`
```bash
Host macmini-lan
    HostName xxx
    User ryou

Host macmini-ts
    HostName xxx
    User ryou

Host macmini-vibe
    HostName xxx
    User ryou
    RequestTTY yes
    RemoteCommand env LANG=en_US.UTF-8 LC_CTYPE=en_US.UTF-8 /opt/homebrew/bin/tmux -u -L vibe new -A -s vibe
```
---
## 其他推荐
1. 配置 `oh-my-zsh`
2. 配置`opencode` `OMO`插件
3. 配置`oh-my-codex`
4. 掌握`mihomo`


---
## 参考
[^1]: 《第四公民》（[Citizenfour](https://citizenfourfilm.com/)）是一部由劳拉·普瓦特拉斯执导的纪录片，2014年上映。该片记录了爱德华·斯诺登揭秘美国国家安全局大规模监控计划的过程，获第87届奥斯卡最佳纪录长片奖。
[^2]: 《纳瓦尔宝典》（[The Almanack of Naval Ravikant](https://www.navalmanack.com/)）由埃里克·乔根森整理编写，汇集了纳瓦尔·拉维坎特十年间关于财富创造与幸福的原则思考。全书可免费在线阅读。

