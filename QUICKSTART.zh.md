# PonyClaw 快速开始指南

## 1. 安装

### 方式一：从源码编译（推荐）

```bash
# 克隆仓库
git clone https://github.com/lansely/ponyclaw.git
cd ponyclaw

# 安装依赖
make deps

# 编译并安装
make install

# 验证安装
ponyclaw version
```

### 方式二：直接编译运行

```bash
# 编译
make build

# 运行（使用 ./build/ponyclaw 代替 ponyclaw 命令）
./build/ponyclaw version
```

## 2. 初始化配置

```bash
# 初始化工作区和配置文件
ponyclaw onboard
```

这会创建：
- `~/.ponyclaw/config.json` - 主配置文件
- `~/.ponyclaw/workspace/` - 工作区目录

## 3. 配置 API Key

编辑配置文件：

```bash
vim ~/.ponyclaw/config.json
```

### 最简配置（使用智谱 AI）

```json
{
  "agents": {
    "defaults": {
      "workspace": "~/.ponyclaw/workspace",
      "model_name": "glm-4",
      "max_tokens": 8192,
      "temperature": 0.7,
      "max_tool_iterations": 20
    }
  },
  "model_list": [
    {
      "model_name": "glm-4",
      "model": "zhipu/glm-4",
      "api_key": "你的智谱API密钥"
    }
  ]
}
```

### 获取 API Key

**推荐的免费/低成本选项：**

1. **智谱 AI (GLM-4)** - 国内用户推荐
   - 注册：https://open.bigmodel.cn/
   - 免费额度：200K tokens/月
   - 配置：`"model": "zhipu/glm-4"`

2. **DeepSeek** - 性价比高
   - 注册：https://platform.deepseek.com/
   - 价格便宜
   - 配置：`"model": "deepseek/deepseek-chat"`

3. **OpenRouter** - 多模型聚合
   - 注册：https://openrouter.ai/
   - 免费额度：200K tokens/月
   - 配置：`"model": "openai/gpt-4"`

4. **Ollama** - 完全本地运行
   - 安装：https://ollama.ai/
   - 完全免费
   - 配置：`"model": "ollama/llama3"`

## 4. 开始使用

### 交互式对话

```bash
ponyclaw agent
```

然后输入你的问题，例如：
```
你好，请介绍一下你自己
```

### 单次问答

```bash
ponyclaw agent -m "2+2等于几？"
```

### 查看状态

```bash
ponyclaw status
```

## 5. 常用功能

### 文件操作

```bash
ponyclaw agent -m "读取当前目录下的 README.md 文件"
ponyclaw agent -m "创建一个 hello.txt 文件，内容是 Hello World"
```

### 网络搜索（需要配置搜索 API）

```bash
ponyclaw agent -m "搜索最新的 Go 语言版本"
```

### 定时任务

```bash
# 列出所有定时任务
ponyclaw cron list

# 添加定时任务
ponyclaw agent -m "每天早上9点提醒我开会"
```

## 6. 高级配置

### 配置多个模型

```json
{
  "model_list": [
    {
      "model_name": "glm-4",
      "model": "zhipu/glm-4",
      "api_key": "你的智谱密钥"
    },
    {
      "model_name": "deepseek",
      "model": "deepseek/deepseek-chat",
      "api_key": "你的DeepSeek密钥"
    }
  ],
  "agents": {
    "defaults": {
      "model_name": "glm-4"
    }
  }
}
```

### 启用网络搜索

```json
{
  "tools": {
    "web": {
      "duckduckgo": {
        "enabled": true,
        "max_results": 5
      }
    }
  }
}
```

DuckDuckGo 无需 API Key，开箱即用！

### 配置心跳任务

编辑 `~/.ponyclaw/workspace/HEARTBEAT.md`：

```markdown
# 周期性任务

- 检查未读邮件
- 查看今天的日程安排
```

启用心跳：

```json
{
  "heartbeat": {
    "enabled": true,
    "interval": 30
  }
}
```

## 7. 聊天平台集成

### Telegram Bot

1. 在 Telegram 中找到 @BotFather
2. 发送 `/newbot` 创建机器人
3. 获取 token
4. 配置：

```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "你的Bot Token",
      "allow_from": ["你的Telegram用户ID"]
    }
  }
}
```

5. 启动网关：

```bash
ponyclaw gateway
```

### Discord Bot

1. 访问 https://discord.com/developers/applications
2. 创建应用并添加 Bot
3. 启用 MESSAGE CONTENT INTENT
4. 配置：

```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "你的Bot Token",
      "allow_from": ["你的Discord用户ID"]
    }
  }
}
```

5. 启动网关：

```bash
ponyclaw gateway
```

## 8. 常见问题

### Q: 如何查看日志？

```bash
# 启用调试日志
ponyclaw agent -d -m "你的问题"
```

### Q: 配置文件在哪里？

```bash
# 主配置
~/.ponyclaw/config.json

# 工作区
~/.ponyclaw/workspace/
```

### Q: 如何更新？

```bash
cd ponyclaw
git pull
make install
```

### Q: 如何卸载？

```bash
# 删除二进制文件
rm $(which ponyclaw)

# 删除配置（可选）
rm -rf ~/.ponyclaw
```

### Q: API 调用失败怎么办？

1. 检查 API Key 是否正确
2. 检查网络连接
3. 查看错误信息：`ponyclaw agent -d -m "测试"`
4. 尝试切换到其他模型

### Q: 如何使用本地模型（Ollama）？

```bash
# 1. 安装 Ollama
# macOS: brew install ollama
# Linux: curl -fsSL https://ollama.ai/install.sh | sh

# 2. 启动 Ollama
ollama serve

# 3. 下载模型
ollama pull llama3

# 4. 配置 PonyClaw
{
  "model_list": [
    {
      "model_name": "llama3",
      "model": "ollama/llama3"
    }
  ],
  "agents": {
    "defaults": {
      "model_name": "llama3"
    }
  }
}

# 5. 使用
ponyclaw agent -m "你好"
```

## 9. 下一步

- 查看完整功能文档：[docs/FEATURES.zh.md](docs/FEATURES.zh.md)
- 查看详细 README：[README.zh.md](README.zh.md)
- 探索技能系统：`ponyclaw skills list`
- 加入社区讨论：GitHub Discussions

## 10. 示例对话

```bash
# 代码生成
ponyclaw agent -m "用 Python 写一个快速排序算法"

# 文件操作
ponyclaw agent -m "列出当前目录下所有 .go 文件"

# 数据分析
ponyclaw agent -m "分析这个 CSV 文件的数据分布"

# 任务规划
ponyclaw agent -m "帮我制定一个学习 Go 语言的计划"

# 问题解答
ponyclaw agent -m "解释一下 Go 的 goroutine 是什么"
```

祝你使用愉快！🚀
