# Atlassian AI Gateway 代理 🚀

欢迎使用 Atlassian AI Gateway 代理！这是一个与 OpenAI API 兼容的代理服务，它能将您的请求安全地路由到 Atlassian 的 AI Gateway。这意味着您可以将各种支持 OpenAI API 的工具和应用，无缝对接到 Atlassian 提供的强大 AI 模型上！


## ✨ 主要功能

- **OpenAI 兼容** 🤖: 完全兼容 `/v1/chat/completions` 和 `/v1/models` 接口，即插即用。
- **多模型支持** 🧠: 支持来自 Anthropic, Google, 和 AWS Bedrock 的多种前沿模型。
- **强大的管理面板** ⚙️: 提供 Web UI 来管理 Atlassian 凭证、生成和查看 API 密钥、修改管理员密码等。
- **凭证管理** 🔑: 通过简洁的 Web 界面，轻松管理多个 Atlassian 凭证（邮箱/Token），实现负载均衡和高可用。
- **数据库持久化** 💾: 所有凭证和配置都安全地存储在 SQLite 数据库中。
- **Docker 化部署** 🐳: 提供 `Dockerfile`，一键构建和部署，轻松搞定！
- **首次运行保护** 🔐: 首次启动时会自动生成一个安全的管理员密码，并提示您修改。

## 🐳 快速入门（使用 Docker）

按照以下步骤，您可以在几分钟内启动并运行代理服务。

**1. 构建 Docker 镜像**

在项目根目录中打开终端，运行以下命令：

```bash
docker build -t atlassian-proxy .
```

**2. 运行 Docker 容器**

使用以下命令启动代理服务。我们通过挂载一个卷来确保存储凭证的数据库文件 (`database.db`) 不会丢失。

```bash
docker run -d -p 8000:8000 --name atlassian-proxy-container -v $(pwd)/data:/app/data atlassian-proxy
```

> **注意**: Windows 用户请使用 `mkdir data` 创建一个 `data` 目录，然后将 `$(pwd)/data` 替换为该目录的绝对路径。

**3. 获取初始管理员密码**

首次运行时，服务会生成一个临时管理员密码。请查看容器的日志来获取它：

```bash
docker logs atlassian-proxy-container
```

您会看到类似下面的输出：

```
🔐 初始管理员密码: some-random-password-123
请在首次登录后立即修改此密码
```

请复制这个密码，您马上会用到它！

## 📖 使用指南

**1. 登录管理界面**

打开浏览器，访问 `http://localhost:8000/admin/login`。使用上一步获取的初始密码登录。首次登录后，系统会强制您修改密码。

**2. 添加 Atlassian 凭证**

修改密码后，您会进入凭证管理页面。在这里，您可以添加您的 Atlassian 账户凭证（邮箱和 API Token）。可以添加多个凭证，代理会在它们之间进行轮询，以提高请求成功率。

**3. 生成并获取 API 密钥**

在凭证管理页面，找到 API 密钥部分，点击 “**Generate New Token**” 按钮。系统会为您生成一个唯一的 API 密钥并显示在页面上。**请复制并妥善保管这个密钥**，它将用于访问代理服务。

**4. 使用代理服务**

现在，您可以将任何 OpenAI 客户端指向您的本地代理地址了！

- **API Base URL**: `http://localhost:8000/v1`
- **API Key**: 您在第 3 步生成的 API 密钥。

这是一个使用 `curl` 的例子。请记得将 `YOUR_GENERATED_API_KEY` 替换为您自己的密钥。

```bash
curl -X POST http://localhost:8000/v1/chat/completions \
-H "Content-Type: application/json" \
-H "Authorization: Bearer YOUR_GENERATED_API_KEY" \
-d '{
  "model": "anthropic:claude-3-5-sonnet-v2@20241022",
  "messages": [
    {
      "role": "user",
      "content": "你好，世界！"
    }
  ]
}'
```

## ⚙️ 配置

您可以通过环境变量来配置服务：

- `PORT`: 服务监听的端口，默认为 `8000`。

例如，要在 `8080` 端口上运行：

```bash
docker run -d -p 8080:8080 -e PORT=8080 --name atlassian-proxy-container -v $(pwd)/data:/app/data atlassian-proxy
```

## 🤖 支持的模型

本代理支持以下通过 Atlassian AI Gateway 提供的模型：

- `anthropic:claude-3-5-sonnet-v2@20241022`
- `anthropic:claude-3-7-sonnet@20250219`
- `anthropic:claude-sonnet-4@20250514`
- `anthropic:claude-opus-4@20250514`
- `google:gemini-2.0-flash-001`
- `google:gemini-2.5-pro-preview-03-25`
- `google:gemini-2.5-flash-preview-04-17`
- `bedrock:anthropic.claude-3-5-sonnet-20241022-v2:0`
- `bedrock:anthropic.claude-3-7-sonnet-20250219-v1:0`
- `bedrock:anthropic.claude-sonnet-4-20250514-v1:0`
- `bedrock:anthropic.claude-opus-4-20250514-v1:0`

---

希望您玩得开心！🎉 如果有任何问题或建议，欢迎提出 Issue！
