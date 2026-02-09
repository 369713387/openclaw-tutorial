# OpenClaw Notion 集成教程

> 本教程介绍如何在 OpenClaw 中集成 Notion，实现 AI 与 Notion 数据库的无缝交互。

## 目录

- [概述](#概述)
- [前置准备](#前置准备)
- [配置步骤](#配置步骤)
- [使用示例](#使用示例)
- [高级用法](#高级用法)
- [故障排查](#故障排查)

## 概述

### 什么是 Notion 集成？

Notion 集成允许 OpenClaw 通过 Notion API 直接与你的 Notion 工作区交互，实现：

- 在 Notion 数据库中创建、查询、更新和删除记录
- 自动将 AI 对话内容同步到 Notion 笔记
- 通过 AI 查询和分析 Notion 数据库内容
- 自动化工作流程（如定期总结、任务管理等）

### 应用场景

| 场景 | 描述 |
|------|------|
| **项目管理** | AI 自动创建项目任务卡片，跟踪进度，生成报告 |
| **会议记录** | AI 将会议对话自动整理并保存到 Notion |
| **知识管理** | AI 查询知识库，回答问题，并记录新知识 |
| **任务跟踪** | AI 根据对话内容自动创建和更新待办事项 |
| **日记笔记** | AI 帮助整理每日笔记，自动归类和标签 |

### 架构说明

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│  用户渠道    │ ─────▶ │  OpenClaw   │ ─────▶ │  Notion API │
│ (Discord)   │         │   Gateway   │         │             │
└─────────────┘         └─────────────┘         └─────────────┘
                              │
                              ▼
                       ┌─────────────┐
                       │  AI 模型     │
                       │ (Claude/GPT)│
                       └─────────────┘
```

## 前置准备

### 1. Notion 账号

确保你拥有一个 Notion 账号，并已登录。

### 2. 创建 Notion Integration

Integration 是连接 OpenClaw 与 Notion 的桥梁。

#### 步骤一：进入 Integration 设置

1. 访问 [Notion My Integrations](https://www.notion.so/my-integrations)
2. 点击 **"New integration"** 按钮

#### 步骤二：填写基本信息

| 字段 | 说明 | 示例 |
|------|------|------|
| Name | 集成名称 | `OpenClaw AI` |
| Associated workspace | 关联的工作区 | 选择你的工作区 |
| Type | 类型 | 选择 `Internal` |
| Capabilities | 能力 | 勾选需要的权限 |

#### 步骤三：配置权限

根据你的需求配置以下权限：

- **Read content**: 读取页面和数据库内容
- **Update content**: 更新页面和数据库内容
- **Insert content**: 创建新页面和数据库记录

#### 步骤四：保存并获取 Token

创建完成后，你会看到 **"Internal Integration Token"**：

```
secret_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

> **重要提示**：请妥善保存这个 Token，它只会显示一次！

### 3. 获取数据库 ID

#### 方法一：从 URL 获取

1. 打开你要集成的 Notion 数据库页面
2. 复制 URL，例如：
   ```
   https://www.notion.so/your-workspace/[database-id]?v=xxxxx
   ```
3. `[database-id]` 部分就是数据库 ID，通常是 32 个字符

#### 方法二：通过 API 获取

如果你有权限，可以通过 API 列出所有数据库：

```bash
curl https://api.notion.com/v1/databases \
  -H "Authorization: Bearer YOUR_INTEGRATION_TOKEN" \
  -H "Notion-Version: 2022-06-28"
```

### 4. 连接 Integration 到数据库

**这一步经常被忽略！**

1. 打开你的 Notion 数据库页面
2. 点击右上角的 **"..."** 菜单
3. 选择 **"Add connections"**
4. 找到你创建的 Integration（如 "OpenClaw AI"）
5. 点击添加

> 如果不完成这一步，即使 Token 正确，API 也会返回 403 错误。

## 配置步骤

### 1. 环境变量配置

创建或编辑 `.env` 文件：

```bash
# Notion API 配置
NOTION_TOKEN=secret_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
NOTION_DATABASE_ID=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### 2. OpenClaw 配置

在 `openclaw.config.yaml` 中添加 Notion 技能：

```yaml
skills:
  - name: notion
    enabled: true
    config:
      databaseId: ${NOTION_DATABASE_ID}
```

### 3. 安装 Notion 技能（如需要）

如果 OpenClaw 没有内置 Notion 支持，可以通过 ClawHub 安装：

```bash
openclaw skills install notion-skill
```

## 使用示例

### 示例 1：创建页面

**用户输入：**
```
在 Notion 中创建一个新页面，标题是"项目计划"，内容包含本周任务
```

**AI 执行：**
```javascript
// AI 自动生成的 API 调用
const response = await notion.pages.create({
  parent: {
    database_id: process.env.NOTION_DATABASE_ID,
  },
  properties: {
    title: {
      title: [
        {
          text: {
            content: "项目计划",
          },
        },
      ],
    },
    status: {
      select: {
        name: "进行中",
      },
    },
  },
});
```

### 示例 2：查询数据库

**用户输入：**
```
查询 Notion 中所有状态为"待办"的任务
```

**AI 执行：**
```javascript
const response = await notion.databases.query({
  database_id: process.env.NOTION_DATABASE_ID,
  filter: {
    property: "状态",
    select: {
      equals: "待办",
    },
  },
});
```

### 示例 3：更新记录

**用户输入：**
```
将标题为"项目计划"的任务状态更新为"已完成"
```

**AI 执行：**
```javascript
// 1. 先查询找到记录
const page = await findPageByTitle("项目计划");

// 2. 更新记录
await notion.pages.update({
  page_id: page.id,
  properties: {
    状态: {
      select: {
        name: "已完成",
      },
    },
  },
});
```

### 示例 4：实际对话场景

#### 场景一：项目管理助手

```
用户: 帮我在 Notion 创建一个新的任务：完成 API 文档编写，优先级高，截止日期本周五

AI: 好的，我已经在 Notion 中创建了任务：
   - 标题：完成 API 文档编写
   - 优先级：高
   - 截止日期：2024-01-12
   - 状态：待办

   任务已成功添加到你的项目管理数据库中。
```

#### 场景二：会议记录整理

```
用户: 把今天的会议记录整理到 Notion，参与人有 Alice 和 Bob，讨论了新功能的架构设计

AI: 已将会议记录保存到 Notion：
   - 日期：2024-01-08
   - 参与人：Alice, Bob
   - 主题：新功能架构设计
   - 关键点：已提取并结构化保存

   查看链接：https://notion.so/xxx
```

#### 场景三：知识库查询

```
用户: 查询我们的 Notion 知识库，看看之前是怎么处理类似问题的

AI: 我在知识库中找到了 3 条相关记录：

   1. [2023-12-15] 数据库性能优化方案
      - 使用索引优化
      - 分表策略

   2. [2023-11-20] API 响应时间优化
      - 缓存层设计
      - 异步处理

   3. [2023-10-05] 类似问题的完整解决方案
      - 详细的实施步骤

   需要我展开其中哪一条的详细内容？
```

## 高级用法

### 1. 与消息渠道结合

将 Notion 集成与 Discord/Telegram 等渠道结合：

```yaml
# openclaw.config.yaml
channels:
  discord:
    enabled: true
    commands:
      - name: "notion"
        description: "与 Notion 交互"
        handler: "notion-skill"
```

**使用场景：**
```
Discord 用户: /notion create "Bug 修复：登录问题"
AI: ✅ 已在 Notion 创建任务卡片
```

### 2. 定时任务同步

使用 Cron 功能自动同步数据：

```yaml
cron:
  - name: "daily-summary"
    schedule: "0 18 * * 1-5"  # 工作日 18:00
    action: "notion-skill:daily-summary"
    params:
      databaseId: "${NOTION_DATABASE_ID}"
```

**效果：** 每天 18:00 自动生成当日工作总结并保存到 Notion

### 3. 自定义模板

为不同类型的 Notion 页面定义模板：

```javascript
// 模板配置
const templates = {
  meeting: {
    properties: {
      类型: { select: { name: "会议记录" } },
      日期: { date: { start: new Date().toISOString() } },
    },
  },
  task: {
    properties: {
      类型: { select: { name: "任务" } },
      状态: { select: { name: "待办" } },
    },
  },
};
```

### 4. 多数据库集成

配置多个数据库：

```yaml
skills:
  - name: notion
    config:
      databases:
        tasks: "TASK_DATABASE_ID"
        notes: "NOTES_DATABASE_ID"
        meetings: "MEETING_DATABASE_ID"
```

**使用：**
```
用户: 在会议数据库中创建一条记录
AI: 好的，正在使用会议数据库...
```

## 故障排查

### 常见错误

#### 错误 1：403 Forbidden

```
APIError: 403 {"code":"object_not_found","message":"The API does not have access to this resource."}
```

**原因：** Integration 未连接到数据库

**解决方案：**
1. 打开 Notion 数据库
2. 点击 "..." → "Add connections"
3. 选择你的 Integration

#### 错误 2：401 Unauthorized

```
APIError: 401 {"code":"unauthorized","message":"Invalid integration token"}
```

**原因：** Token 无效或过期

**解决方案：**
1. 重新生成 Integration Token
2. 更新 `.env` 文件
3. 重启 OpenClaw Gateway

#### 错误 3：400 Bad Request

```
APIError: 400 {"code":"validation_error","message":"..."}
```

**原因：** 请求参数格式错误

**解决方案：**
1. 检查数据库属性名称是否匹配
2. 确认属性类型正确（text/number/date 等）
3. 查看 Notion API 文档确认格式

#### 错误 4：429 Rate Limited

```
APIError: 429 {"code":"rate_limited","message":"..."}
```

**原因：** 请求频率过高

**解决方案：**
1. 添加请求延迟
2. 实现重试机制
3. 考虑升级 Notion 计划

### 调试方法

#### 启用详细日志

```bash
openclaw logs --level debug
```

#### 测试 API 连接

```bash
curl -X GET https://api.notion.com/v1/users/me \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Notion-Version: 2022-06-28"
```

#### 验证数据库访问

```bash
curl -X POST https://api.notion.com/v1/databases/YOUR_DATABASE_ID/query \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Notion-Version: 2022-06-28" \
  -H "Content-Type: application/json" \
  -d '{}'
```

### 获取帮助

如果以上方法都无法解决问题：

1. 检查 [Notion API 状态页](https://status.notion.so/)
2. 查看 [Notion API 文档](https://developers.notion.com/reference)
3. 在 [OpenClaw Discord](https://discord.gg/clawd) 社区提问
4. 提交 [GitHub Issue](https://github.com/openclaw/openclaw/issues)

## 附录

### Notion API 限制

| 项目 | 免费版 | 付费版 |
|------|--------|--------|
| 请求速率 | 3 请求/秒 | 取决于计划 |
| 数据库大小 | 有限制 | 更高限制 |

### 推荐数据库结构

#### 任务数据库

| 属性名 | 类型 | 说明 |
|--------|------|------|
| 标题 | Title | 任务名称 |
| 状态 | Select | 待办/进行中/已完成 |
| 优先级 | Select | 高/中/低 |
| 截止日期 | Date | 任务截止时间 |
| 负责人 | Person | 任务负责人 |
| 描述 | Text | 详细描述 |

#### 会议记录数据库

| 属性名 | 类型 | 说明 |
|--------|------|------|
| 标题 | Title | 会议主题 |
| 日期 | Date | 会议日期 |
| 参与人 | People | 参会人员 |
| 关键点 | Text | 会议要点 |
| 行动项 | Text | 待办事项 |

## 参考资源

- [Notion API 官方文档](https://developers.notion.com/reference)
- [Notion Integration 指南](https://www.notion.com/zh-cn/help/create-integrations-with-the-notion-api)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw 官方文档](https://docs.openclaw.ai)

---

**祝你使用愉快！** 如有疑问，欢迎在社区交流。
