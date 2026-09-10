# MDCX 修复版

基于 [Hazard804/mdcx](https://github.com/Hazard804/mdcx) 源码构建的修复版。

## 修复内容

### 修复 1：CA 证书（✅ 已实现）
- **问题**：curl_cffi 打包时未包含 cacert.pem，导致 HTTPS 请求失败
- **修复**：系统级安装 `ca-certificates` 包
- **验证**：容器内 HTTPS 请求正常

### 修复 2：prestige.py 异常处理（✅ 已实现）
- **问题**：`get_json` 调用可能抛出未捕获异常
- **修复**：添加 `try/except` 包裹，异常转为 `CralwerException`

## 构建方式

### 源码构建（推荐）

\`\`\`bash
git clone https://github.com/namebao18/mdcx-fixed.git
cd mdcx-fixed
docker compose -f docker/docker-compose.source.yml up -d --build
\`\`\`

### 使用预构建镜像

\`\`\`bash
docker pull stainless403/mdcx-fixed:latest
docker run -d --name mdcx -p 5800:5800 stainless403/mdcx-fixed:latest
\`\`\`

## 文件说明

| 文件 | 说明 |
|------|------|
| `docker/Dockerfile.source` | 基于源码构建的 Dockerfile |
| `docker/docker-compose.source.yml` | 源码构建的 compose 配置 |
| `docker/Dockerfile` | 基于二进制修复的 Dockerfile |
| `docker/docker-compose.yml` | 二进制修复的 compose 配置 |
| `FIXES.md` | 修复详情 |
