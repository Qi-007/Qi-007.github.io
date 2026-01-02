---
title: 🚀 Claude Code 使用指南
published: 2025-12-28
description: "Claude Code 初试"
image: "https://aircheetah.dpdns.org/PicGo/Claude.jpg"
tags: ["AI Tools", "Claude Code"]
category: Guides
draft: false
lang: 'zh-CN'
---

最近了解了一下各种势头正盛的AI编程工具，由于GPT Plus到期，Codex短期时间内只得作罢😿，先来总结一下Claude Code的使用技巧

---

## 📁 基础命令

### `/init`

📌 **初始化项目**

+ 自动生成项目级 `CLAUDE.md`
+ 用于记录项目背景、约束、约定风格等（长期记忆）

---

### `/compact`

🧠 **压缩上下文，降低 Token 消耗**

+ 可附加指令，控制压缩方式  
例如：`/compact 只保留需求和最终结论`

---

### `/clear`

🧹 **清空当前对话，开启新任务**

+ 保持上下文干净
+ 强烈建议：**一个任务一个上下文**

---

### 思考强度控制

可在 prompt 中显式控制模型推理深度：

+ `think` → 正常思考
+ `think hard` → 深度分析
+ `ultrathink` → 极限推理（慎用，耗 token）

---

## 🖥️ 临时命令行 & 一次性对话

### `!` 临时命令行模式

+ 执行 shell 命令
+ **执行结果会加入 Claude 上下文**
+ 避免重复执行命令 👍

---

### `claude -p "..."`

🧪 **一次性临时对话**

+ 不污染当前会话
+ 非常适合快速验证想法

---

### `/ide`

链接IDE如VSCode等（需要提前安装插件），连接后显示代码修改前后差异</font>

---

## 🧠 记忆系统（Memory）

### `# 进入记忆模式`

+ 支持 **项目级** / **用户级**
+ 内容最终写入 `CLAUDE.md`
+ 适合保存：
  + 项目约定
  + 架构原则
  + 长期偏好

---

## 🔌 MCP（Model Context Protocol）

### 使用 `context7` 作为 MCP Server

#### 项目级

```plain
claude mcp add context7 -- npx @upstash/context7-mcp
```

#### 用户级

```plain
claude mcp add context7 --scope user -- npx @upstash/context7-mcp
```

#### 删除 MCP

```plain
claude mcp remove context7
```

📌 **用途**

+ 扩展 Claude 的上下文感知能力
+ 提供跨项目 / 长期知识支持

---

## 🔐 权限系统（Permissions）

### `/permissions`

+ 自定义规则：
  + `allow`
  + `deny`

⚠️ **最高权限模式（危险）**

```plain
claude --dangerously-skip-permissions
```

👉 仅建议在**完全信任的本地环境**使用

---

## 🧩 自定义命令（Commands）

📂 在 `~/.claude/commands/` 中创建自定义命令文件

示例：

```plain
~/.claude/commands/code_review.md
```

文件中直接用自然语言描述命令用途即可，例如：

“对当前 Git diff 进行代码审查，关注潜在 bug 与工程风险”

之后在claude code就可以直接使用 /code_review 命令

---

## 🪝 Hooks

Hooks 允许你在 **Claude Code 会话生命周期**的关键节点插入自动化逻辑  
👉 非常适合：**格式校验、环境初始化、合规检查**

### ✅ 支持的 9 大 Hook 事件

1. `SessionStart` – 会话启动
2. `UserPromptSubmit` – 用户提交 prompt
3. `PreToolUse` – 调用工具前
4. `PostToolUse` – 工具执行完成后
5. `Notification` – 内部通知事件
6. `SubagentStop` – 子 Agent 结束
7. `PreCompact` – 上下文压缩前
8. `Stop` – Claude 停止生成
9. `SessionEnd` – 会话结束

---

### 🧪 示例：每次修改代码后自动检查格式（Prettier）

在 `~/.claude/settings.json` 中配置：

```plain
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|MultiEdit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --check ."
          }
        ]
      }
    ]
  }
}
```

💡 **效果**：Claude 改完代码 → 自动跑 prettier ✅

---

## 🤖 SubAgent（子 Agent 拆分复杂任务）

### `/agents`

使用自然语言定义 Agent 职责即可

#### 示例 Agent

**Diff Agent**

你是一个 **Git 分支差异分析 Agent**，负责客观提取代码变更事实。

**Bug Agent**

你是一个 **代码缺陷分析与风险评估 Agent**，判断是否引入 bug 或工程风险。

📌 当你发布任务时，例如：

“帮我审核 `feature/radar-time-sync` 分支，是否可以合并到 `main`”

Claude 会**自动拆分任务并分发给子 Agent** ，也是当上赛博老板了🤨🫵🤖💦

---

## 🐙 GitHub 集成

🔗 官方 CLI

+ GitHub CLI：[https://github.com/cli/cli](https://github.com/cli/cli)
+ CLI官方文档：[https://docs.github.com/zh/github-cli/github-cli/quickstart](https://docs.github.com/zh/github-cli/github-cli/quickstart)

### 常用命令

```plain
gh auth login   # GitHub 身份验证
gh repo list   # 查看名下仓库
```

📌 Claude Code 可：

+ 修复 Issue

+ 提交代码

+ 直接 push 回 GitHub  

  👉 真 · AI 工程搭子 🧑‍💻🤝🤖

---

## ⏪ 会话回退与导出

### `/resume`

+ 查找历史话题
+ 官方只能回退**对话内容**

### 🧠 进阶：回退代码 + 对话（ccundo）

🔗 [CCUNDO GitHub地址](https://github.com/RonitSachdev/ccundo)

```plain
npm install -g ccundo  // 安装ccundo
ccundo list  // 列出对话记录
ccundo undo <id>  // 回退对话和代码内容
```

---

### `/export`

📤 导出当前对话内容，可以保存为文件或者与其他模型chat

---

## 🖼️ Claude 可视化工具

### Claudia

claudia 实现了 claude code 的多种功能，小白直接</font>宝宝巴士启动 🚍

🔗 [Claudia GitHub地址](https://github.com/who10086/claudia-global)

✨ 特点：

+ 可视化 Claude Code
+ **时间轴回退（对话 + 文件）**

📌 支持：

+ Claude 账号直接登录
+ 或使用 API Key
  + `ANTHROPIC_AUTH_TOKEN`
  + `ANTHROPIC_BASE_URL`
+ 在 Claude Code 中可用 `/status` 查看环境变量

---

## ✅ 总结

Claude Code ≠ 聊天工具  
它是一个：

**可编排 · 可扩展 · 可自动化 · 可回溯的 AI 工程系统**

更多玩法还可以看[Claude Code官方文档](https://code.claude.com/docs/zh-CN/overview)，当然解决了基本的使用问题之后就该思考如何减少token消耗，获取各种最新文档的API调用方法...... 让Claude Code清清爽爽漂漂亮亮的完成工作，这些都可以通过接入其他开源项目实现，下篇细说...
