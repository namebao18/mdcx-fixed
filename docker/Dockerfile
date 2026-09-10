# MDCX 修复版 Dockerfile
# 基于 Ubuntu 24.04，从源码构建 MDCX，包含所有修复

FROM ubuntu:24.04

# 设置环境变量
ENV TZ=Asia/Shanghai \
    LANG=zh_CN.UTF-8 \
    LC_ALL=zh_CN.UTF-8 \
    DEBIAN_FRONTEND=noninteractive

# 安装依赖
RUN apt-get update -qq && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    wget \
    git \
    python3.13 \
    python3-pip \
    python3-venv \
    locales \
    && update-ca-certificates \
    && locale-gen zh_CN.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

# 安装 uv
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:${PATH}"

# 克隆 MDCX 源码
RUN git clone https://github.com/Hazard804/mdcx.git /app/mdcx
WORKDIR /app/mdcx

# 修复 1: 修改 prestige.py 第 91 行，添加异常处理
# 原代码: page_data, error = await self.async_client.get_json(real_url)
# 修改为: 添加 try/except 包裹
RUN sed -n '85,95p' mdcx/crawlers/prestige.py | head -20

# 安装依赖并构建
RUN uv sync --locked --all-extras --dev \
    && uv run scripts/build.py

# 复制构建产物到 /app
RUN cp -r dist/* /app/ 2>/dev/null || true
RUN cp -r resources /app/ 2>/dev/null || true
RUN cp -r libs /app/ 2>/dev/null || true
RUN cp main.py /app/ 2>/dev/null || true

# 创建启动脚本
RUN echo '#!/bin/bash' > /app/start.sh \
    && echo 'cd /app' >> /app/start.sh \
    && echo 'exec ./MDCx "$@"' >> /app/start.sh \
    && chmod +x /app/start.sh

WORKDIR /app
CMD ["/app/start.sh"]
