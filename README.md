# 🫟 1Panel 第三方 App Store

由于常常折腾Homelab,为了方便迅速部署,于是有了自建维护的 **1Panel 第三方应用商店仓库**想法，GitHub上面类似项目很多,APP多有参考,部分直接收录,适配个人常用的容器化应用与预设配置.

> 本项目旨在补充官方 App Store，适配个人常用需求场景。

---
### 1Panel 制作第三方应用

1.官方文档:[📚如何制作1Panel第三方应用](https://github.com/1Panel-dev/appstore/wiki/%E5%A6%82%E4%BD%95%E6%8F%90%E4%BA%A4%E8%87%AA%E5%B7%B1%E6%83%B3%E8%A6%81%E7%9A%84%E5%BA%94%E7%94%A8)

2.便利工具:使用[1Panel-Tools](https://github.com/arch3rPro/1Panel-Appstore/blob/main/apps/1panel-tools/README.md)工具制作

---
### 使用方法
#### 📋 添加脚本到 1Panel 计划任务及自动刷新本地应用
```bash
变量创建
# 创建配置文件夹
sudo mkdir -p /opt/1panel/task/api-config

# 写入配置（请替换为你真实的 API_KEY）
sudo tee /opt/1panel/task/api-config/api.conf <<EOF
API_KEY="你的真实API_KEY(1panel 面板创建,白名单IP:127.0.0.1)"
PANEL_URL="https://你的面板绑定域名" (域名最后不要带/)
EOF
# 将你的面板域名指向本地回环地址
echo "127.0.0.1 你的面板绑定域名" | sudo tee -a /etc/hosts 

# 赋予权限
sudo chmod 600 /opt/1panel/task/api-config/api.conf
sudo chown root:root /opt/1panel/task/api-config/api.conf
```
```bash


#!/bin/bash
set -euo pipefail

# --- 1. 配置加载 ---
CONF_FILE="/opt/1panel/task/api-config/api.conf"
APP_DIR="/opt/1panel/resource/apps/local"
TMP_DIR="/tmp/1panel_appstore_tmp"
GIT_REPO="https://github.com/Ray2023m/1Panel-appstore.git"

# 颜色定义
GREEN='\033[0;32m'
RED='\033[0;31m'
NC='\033[0m'

log_info() { echo -e "${GREEN}[INFO] $1${NC}"; }
log_error() { echo -e "${RED}[ERROR] $1${NC}"; }

# 检查并加载
if [ ! -f "$CONF_FILE" ]; then
    log_error "找不到配置文件: $CONF_FILE"
    log_info "请检查文件是否存在或权限是否正确（当前执行用户: $(whoami)）"
    exit 1
fi

# 加载变量
source "$CONF_FILE"

# 再次确认变量是否为空
if [ -z "${API_KEY:-}" ]; then
    log_error "API_KEY 载入失败，请确认 $CONF_FILE 内容格式正确"
    exit 1
fi

# --- 2. 拉取数据逻辑 ---
log_info "开始拉取 GitHub 应用商店数据..."
rm -rf "$TMP_DIR"
mkdir -p "$TMP_DIR"

if git clone --depth=1 "$GIT_REPO" "$TMP_DIR/appstore"; then
    log_info "✅ GitHub 数据下载成功"
else
    log_error "❌ GitHub 数据下载失败"
    exit 1
fi

mkdir -p "$APP_DIR"
if cp -rf "$TMP_DIR/appstore/apps/"* "$APP_DIR/"; then
    log_info "✅ 文件拷贝完成"
else
    log_error "❌ 文件拷贝失败"
    exit 1
fi
rm -rf "$TMP_DIR"

# --- 3. 调用 API 同步 ---
log_info "正在通知 1Panel 更新本地应用列表..."
TIMESTAMP=$(date +%s)
TOKEN=$(echo -n "1panel${API_KEY}${TIMESTAMP}" | md5sum | awk '{print $1}')

RESPONSE=$(curl -s -w "\n%{http_code}" -X POST "${PANEL_URL}/api/v2/apps/sync/local" \
  -H "1Panel-Token: ${TOKEN}" \
  -H "1Panel-Timestamp: ${TIMESTAMP}" \
  -H "Content-Type: application/json" \
  -d '{}')

HTTP_BODY=$(echo "$RESPONSE" | sed '$d')
HTTP_CODE=$(echo "$RESPONSE" | tail -n1)

if [ "$HTTP_CODE" -eq 200 ] && [[ "$HTTP_BODY" == *"\"code\":200"* ]]; then
    log_info "🚀 1Panel 本地应用刷新成功！"
    echo "响应数据: $HTTP_BODY"
else
    log_error "❌ API 执行异常 (HTTP $HTTP_CODE)"
    echo "响应详情: $HTTP_BODY"
    exit 1
fi

log_info "✨ 所有操作已完成！"
```
### 项目统计
![Alt](https://repobeats.axiom.co/api/embed/15348dc305f26710da5ba1f38b3504ea1955ca78.svg "Repobeats analytics image")
