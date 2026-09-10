# MDCX 修复版

基于 [Hazard804/mdcx](https://github.com/Hazard804/mdcx) / [stainless403/mdcx-builtin-gui-base](https://github.com/stainless403/mdcx-builtin-gui-base) 修复的刮削工具。

## 修复内容

### 修复 1：CA 证书（已实现）
- **问题**：原镜像缺少 CA 证书，HTTPS 请求失败（curl 错误 77）
- **修复**：安装 `ca-certificates` 包
- **验证**：容器内 HTTPS 请求正常返回 200

### 修复 2：异常处理（待实现）
- **问题**：`prestige.py:91` 未捕获异常，单个文件失败导致整个任务崩溃
- **状态**：因原版使用 PyInstaller 打包（Python 3.13），无法直接修改字节码
- **临时方案**：已记录问题，后续有源码后可彻底修复

## 快速部署

### 方式一：Docker Compose（推荐）

\`\`\`yaml
# docker-compose.yml
version: '3.8'

services:
  mdcx:
    image: stainless403/mdcx-fixed:latest  # 修复版镜像
    container_name: mdcx
    restart: unless-stopped
    environment:
      - TZ=Asia/Shanghai
      - PGID=1001
      - PUID=1000
      - WEB_LISTENING_PORT=5800
      - VNC_LISTENING_PORT=5900
      - MDCX_CONFIG_PATH=/mdcx-config
    volumes:
      - './mdcx-config:/mdcx-config'      # 配置目录
      - './mdcx-config/MDCx.config:/app/MDCx.config'
      - './data:/config'
      - './logs:/app/Log'
      - '/vol2/1000/1024:/vol2/1000/1024'  # 媒体库（按需修改）
    shm_size: '2g'
\`\`\`

### 方式二：一键启动

\`\`\`bash
# 1. 创建配置目录
mkdir -p mdcx-config data logs

# 2. 复制原配置（可选）
# cp /path/to/your/config.v2.json mdcx-config/

# 3. 启动
docker compose up -d
\`\`\`

## 验证修复

\`\`\`bash
# 测试 CA 证书
docker exec mdcx curl -s -o /dev/null -w "%{http_code}" https://httpbin.org/get
# 应返回 200
\`\`\`

## 配置说明

| 路径 | 说明 |
|------|------|
| `./mdcx-config` | 主配置目录（持久化） |
| `./mdcx-config/MDCx.config` | MDCx 配置文件 |
| `./data` | 应用数据（演员、海报等） |
| `./logs` | 日志目录 |
| `/vol2/1000/1024` | 媒体库挂载点（按实际路径修改） |

## Web UI

- **地址**：http://localhost:5800
- **VNC**：localhost:5900（可选）

## 原版镜像

- `stainless403/mdcx-builtin-gui-base:v2-d20250909`

## License

MIT
