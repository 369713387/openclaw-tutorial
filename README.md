# OpenClaw 使用教程

> OpenClaw - 开源 AI 助手，让 AI 更好用

## 📝 关于本项目

本项目提供 OpenClaw 的完整使用教程和最佳实践，帮助你快速上手并掌握 OpenClaw 的各种功能。

## 👤 作者信息

- **作者：** Yifun
- **GitHub：** [@369713387](https://github.com/369713387)
- **项目：** [openclaw-tutorial](https://github.com/369713387/openclaw-tutorial)

## 🚀 快速开始

### 安装 OpenClaw

\`\`\`bash
npm install -g openclaw
\`\`\`

### 初始化配置

\`\`\`bash
openclaw configure
\`\`\`

### 启动 Gateway

\`\`\`bash
openclaw gateway start
\`\`\`

## 📚 教程目录

### 基础入门
- [安装与配置](#安装与配置)
- [快速开始](#快速开始)
- [核心概念](#核心概念)

### 高级功能
- [Agent 配置](#agent-配置)
- [技能（Skills）使用](#技能skills-使用)
- [消息渠道集成](#消息渠道集成)
- [定时任务（Cron）](#定时任务cron)
- **[Notion 集成教程](docs/notion-integration.md)** - 详细的 Notion API 集成指南

### 最佳实践
- [项目结构建议](#项目结构建议)
- [常用命令](#常用命令)
- [故障排查](#故障排查)

## 📖 详细教程

### 安装与配置

#### 系统要求
- Node.js >= 18
- npm 或 yarn

#### 安装步骤

1. 全局安装 OpenClaw CLI
\`\`\`bash
npm install -g openclaw
\`\`\`

2. 运行配置向导
\`\`\`bash
openclaw configure
\`\`\`

3. 按提示配置：
   - 模型提供商
   - 消息渠道
   - 工作区路径

### 快速开始

#### 第一个 AI 对话

\`\`\`bash
openclaw chat "你好，介绍一下你自己"
\`\`\`

#### 集成到 Discord

1. 在 Discord 开发者门户创建应用
2. 获取 Bot Token
3. 运行配置命令添加 Discord
\`\`\`bash
openclaw configure --section channels
\`\`\`

## 🔧 常用命令

\`\`\`bash
# 启动 Gateway
openclaw gateway start

# 停止 Gateway
openclaw gateway stop

# 查看状态
openclaw status

# 配置设置
openclaw configure

# 查看日志
openclaw logs

# 安装技能
openclaw skills install <skill-name>
\`\`\`

## 🌟 核心功能

### 1. 多模型支持
- OpenAI (GPT-4, GPT-3.5)
- Anthropic (Claude)
- Google (Gemini)
- 自定义模型

### 2. 多渠道集成
- Discord
- Telegram
- Slack
- Signal
- WhatsApp
- iMessage
- 等等...

### 3. 技能系统
- 自定义技能开发
- ClawHub 技能市场
- 热加载

### 4. 定时任务
- Cron 表达式支持
- 定期提醒
- 自动化工作流

### 5. 记忆系统
- 长期记忆
- 向量搜索
- 自动提取

## 🤝 贡献指南

欢迎贡献！请遵循以下步骤：

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

## 🔗 相关链接

- [OpenClaw 官方文档](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw Discord 社区](https://discord.gg/clawd)
- [ClawHub 技能市场](https://clawhub.com)

---

<div align="center">

**如果这个项目对你有帮助，请给个 ⭐ Star！**

Made with ❤️ by Yifun

</div>
