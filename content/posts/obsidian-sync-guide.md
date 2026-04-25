---
title: "Obsidian 多端同步实战：PVE + WebDAV + Cloudflare Tunnel"
date: 2026-04-25T20:00:00+08:00
draft: false
tags: ["Obsidian", "同步", "自建服务", "PVE", "Cloudflare"]
categories: ["技术"]
summary: "不想用 Obsidian 官方同步（太贵）也不想用 iCloud（太慢）？来看看如何用 PVE 容器 + Rclone WebDAV + Cloudflare Tunnel 实现零成本、全平台无感同步。"
ShowToc: true
TocOpen: true
comments: true
---

## 为什么要折腾同步？

用 Obsidian 记笔记有个绕不开的问题：**多端同步**。

官方的 Obsidian Sync 好用归好用，但一年 $96 实在肉疼。iCloud 同步在 Windows 上体验灾难级。第三方网盘（坚果云、OneDrive）要么速度慢，要么有冲突问题。

所以我选择了自建方案：**PVE 容器 + Rclone WebDAV + Cloudflare Tunnel**。

整体架构非常简单：

```
Obsidian (各设备)
    ↕ HTTPS
Cloudflare Tunnel (内网穿透)
    ↕
Rclone WebDAV (PVE 容器内)
    ↕
本地磁盘 (/opt/obsidian_sync/data)
```

## 环境准备

我的环境是 PVE 下的 Debian 12 LXC 容器。如果你用的是其他 Linux 发行版，命令大同小异。

### 开启 SSH

在 PVE 网页控制台里编辑 SSH 配置：

```bash
nano /etc/ssh/sshd_config
```

找到 `PermitRootLogin`，改为 `yes`，然后重启：

```bash
systemctl restart ssh
```

### 安装基础依赖

```bash
timedatectl set-timezone Asia/Shanghai
apt update && apt upgrade -y && apt install -y curl wget nano ca-certificates unzip
```

## 部署 WebDAV 服务

这里用 **Rclone** 原生提供 WebDAV，不需要 Docker，极其轻量。

### 安装 Rclone

```bash
curl https://rclone.org/install.sh | bash
```

### 创建数据目录

```bash
mkdir -p /opt/obsidian_sync/data
```

### 注册为系统服务

把以下内容写入 systemd，注意把 `myuser` 和 `mypassword` 换成你自己的强密码：

```bash
cat << 'EOF' > /etc/systemd/system/rclone-webdav.service
[Unit]
Description=Rclone WebDAV Server for Obsidian
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone serve webdav /opt/obsidian_sync/data \
  --addr 0.0.0.0:8080 \
  --user myuser \
  --pass mypassword
Restart=on-failure
RestartSec=5
User=root

[Install]
WantedBy=multi-user.target
EOF
```

### 启动服务

```bash
systemctl daemon-reload
systemctl enable rclone-webdav
systemctl start rclone-webdav
```

用 `systemctl status rclone-webdav` 确认状态是绿色的 `active (running)` 就行。

## Cloudflare Tunnel 内网穿透

这一步解决的是"没有公网 IP"的问题。Cloudflare Tunnel 免费、稳定、还自带 HTTPS。

### 安装 cloudflared

```bash
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
dpkg -i cloudflared-linux-amd64.deb
```

### 绑定隧道

在 [Cloudflare Zero Trust](https://one.dash.cloudflare.com/) 后台创建 Tunnel，复制 Token 执行：

```bash
cloudflared service install 你的Token
```

### 配置公网路由

在 Cloudflare 后台的 **Public Hostname** 页面添加记录：

| 设置项 | 值 |
|:---|:---|
| Subdomain | `sync`（或你喜欢的前缀） |
| Domain | 你的域名 |
| Service Type | HTTP |
| URL | `localhost:8080` |

保存后，`https://sync.yourdomain.com` 就直通你的 WebDAV 了。

## Obsidian 客户端配置

在电脑和手机的 Obsidian 里安装 **Remotely Save** 插件。

### 连接参数

| 配置项 | 值 |
|:---|:---|
| 同步方案 | WebDAV |
| 服务器地址 | `https://sync.yourdomain.com` |
| 用户名 | Rclone 配置的 user |
| 密码 | Rclone 配置的 password |

### 推荐配置

- ✅ **端到端加密**：必须开启，所有设备用同一密码
- ✅ **同步书签**：开启
- ❌ **同步配置文件夹**：关闭（防止电脑端和手机端 UI 互相干扰）
- 📋 **冲突处理**：保留最后修改的版本
- 🗑️ **删除文件去向**：系统回收站

## 插件推荐

既然都折腾到这了，顺便分享一下我在双端用的核心插件。

### 全平台必备

| 插件 | 用途 |
|:---|:---|
| **Dataview** | 用类 SQL 语法动态检索库内信息 |
| **Omnisearch** | 全文检索，支持拼音首字母和图片 OCR |

### 电脑端专属

| 插件 | 用途 |
|:---|:---|
| **Linter** | 一键格式化 Markdown，统一排版 |
| **Excalidraw** | 手绘白板，画架构图和思维导图 |

### 手机端专属

| 插件 | 用途 |
|:---|:---|
| **Commander** | 把"主动同步"按钮固定到底部工具栏 |
| **QuickAdd** | 闪念捕捉，一键录入灵感 |

## 实际体验

这套方案跑了一段时间，说说真实感受：

**优点：**
- 💰 **零成本**：全部用免费服务，每年省 $96
- ⚡ **速度快**：Cloudflare 全球 CDN 加速，国内体验也不错
- 🔐 **安全**：端到端加密 + HTTPS，数据在自己手里
- 📱 **全平台**：Windows、macOS、iOS、Android 都能用

**缺点：**
- 🔧 需要一台常开的服务器（PVE、NAS 或云主机都行）
- 📡 依赖 Cloudflare 的稳定性（目前没出过问题）
- ⏱️ 初次同步大仓库需要一点耐心

总的来说，如果你有自己的服务器，这套方案性价比极高。

---

*相关参考：如果你对服务器管理感兴趣，可以看看我之前整理的 PVE 环境搭建笔记。*
