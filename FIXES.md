# 修复记录

## 问题 1：CA 证书缺失

- **现象**：`curl: (77) error setting certificate file: /etc/ssl/certs/ca-certificates.crt`
- **原因**：基础镜像（alpine/debian 精简版）未预装 CA 证书包
- **修复**：`apt-get install -y ca-certificates && update-ca-certificates`
- **验证**：`curl https://httpbin.org/get` → HTTP 200

## 问题 2：prestige.py 异常崩溃

- **现象**：`Exception: 网络请求错误: GET https://www.prestige-av.com/api/search?searchText=... 失败: HTTP 403`
- **原因**：`prestige.py:91` 抛出未捕获异常，导致整个刮削任务停止
- **状态**：**未修复**（PyInstaller 打包，Python 3.13 字节码无法反编译）
- **临时方案**：跳过该网站或单独处理失败文件
- **彻底修复**：需获取源码后重写异常处理

## 问题 3：重复检测误判

- **现象**：已刮削成功的文件被误判为重复并移入失败目录
- **原因**：MDCX 内置重复检测逻辑存在误判
- **状态**：待进一步分析
