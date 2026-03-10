---
name: ollama-install
description: Install and configure Ollama on Linux servers and local machines. TRIGGER when user asks to install Ollama, run Ollama models, or configure Ollama API.
version: 1.0.0
tools: Bash, WebFetch, WebSearch
---

# Ollama 安装与配置

## 触发条件

**适用场景：**
- 用户询问安装 Ollama
- 用户需要在服务器上运行 LLM 模型
- 用户需要配置 Ollama API 访问

**不适用：**
- 已有的其他 AI SDK 使用

## Linux 服务器安装

### 1. 安装依赖

```bash
# Alibaba Cloud Linux / RHEL / CentOS
dnf install -y zstd

# Debian / Ubuntu
apt-get install zstd
```

### 2. 安装 Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 3. 启动服务（监听所有地址）

```bash
export OLLAMA_HOST=0.0.0.0:11434
nohup ollama serve > /tmp/ollama.log 2>&1 &
```

### 4. 验证服务

```bash
# 本地测试
curl http://localhost:11434

# 公网测试（需开放安全组端口）
curl http://<服务器IP>:11434
```

## 下载模型

```bash
# 查看可用模型
curl http://localhost:11434/api/tags

# 下载模型（根据服务器内存选择）
ollama pull qwen3.5:0.8b   # 1GB，适合低内存服务器
ollama pull qwen3.5:9b     # 6.6GB，需要大内存
ollama pull llama3.2:1b   # 1.3GB
```

**注意：** 确保服务器内存大于模型需求。查看内存：

```bash
free -h
```

## API 调用

### 基础聊天

```bash
curl http://<服务器IP>:11434/api/chat \
  -d '{
    "model": "qwen3.5:0.8b",
    "messages": [{"role": "user", "content": "你好"}],
    "stream": false
  }'
```

### Python 调用

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://<服务器IP>:11434/v1",
    api_key="ollama"  # 任意字符串
)

response = client.chat.completions.create(
    model="qwen3.5:0.8b",
    messages=[{"role": "user", "content": "你好"}]
)
print(response.choices[0].message.content)
```

### JavaScript 调用

```javascript
import ollama from 'ollama'

const response = await ollama.chat({
    model: 'qwen3.5:0.8b',
    messages: [{role: 'user', content: '你好'}],
})
console.log(response.message.content)
```

## 阿里云服务器注意事项

1. **安全组配置**：在 ECS 控制台开放 11434 端口（TCP）
2. **内存限制**：1.9GB 内存建议使用 qwen3.5:0.8b 或更小模型
3. **公网访问**：Ollama 默认只监听 127.0.0.1，需配置 OLLAMA_HOST=0.0.0.0:11434

## 常用命令

```bash
# 查看已安装模型
ollama list

# 运行模型（交互模式）
ollama run qwen3.5:0.8b

# 删除模型
ollama rm <模型名>

# 查看运行中的模型
ollama ps
```

## 添加 API 认证（可选）

Ollama 本身不支持内置认证。可通过 Nginx 反向代理添加 Basic Auth：

```bash
# 1. 安装 Nginx
dnf install -y nginx

# 2. 创建密码文件
htpasswd -bc /etc/nginx/.htpasswd ollama <你的密码>

# 3. 配置 Nginx 反向代理
```

认证后调用：

```bash
curl -u ollama:<你的密码> http://<服务器IP>:11434/api/tags
```

## 本地安装（Windows）

```powershell
# PowerShell 安装
irm https://ollama.com/install.ps1 | iex

# 或下载安装包
# https://ollama.com/download/windows

# 验证安装
ollama --version

# 下载模型
ollama pull qwen3.5:0.8b

# 运行模型
ollama run qwen3.5:0.8b
```

## 工作流程

1. **检查环境**：服务器配置、内存、网络
2. **安装 Ollama**：按系统选择对应安装方式
3. **配置网络**：确保 11434 端口可访问
4. **选择模型**：根据内存选择合适的模型
5. **测试 API**：验证服务正常运行
6. **（可选）添加认证**：通过 Nginx 配置