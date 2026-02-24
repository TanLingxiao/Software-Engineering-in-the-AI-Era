# 第2周：编码代理的解剖

## 📋 本周概述

本周我们将深入了解编码代理（Coding Agents）的内部架构和组件。编码代理是能够自主或半自主地完成编程任务的 AI 系统，它们结合了 LLM 的理解能力和工具执行能力。

## 🎯 学习目标

完成本周学习后，你将能够：

- 理解编码代理的核心架构和组件
- 掌握工具使用和函数调用的概念
- 了解模型上下文协议（MCP）的工作原理
- 构建一个简单的编码代理

## 📚 本周主题

1. **代理架构** - 了解编码代理的组成部分
2. **工具使用** - 学习如何让 LLM 调用外部工具
3. **MCP 协议** - 掌握模型上下文协议
4. **实践** - 在 AI IDE 中使用编码代理

## 📖 阅读材料

- [MCP 介绍](https://stytch.com/blog/model-context-protocol-introduction/)
- [MCP 服务器实现示例](https://github.com/modelcontextprotocol/servers)
- [MCP 服务器认证](https://developers.cloudflare.com/agents/guides/remote-mcp-server/#add-authentication)
- [MCP 服务器 SDK](https://github.com/modelcontextprotocol/typescript-sdk/tree/main?tab=readme-ov-file#server)
- [MCP 注册表](https://blog.modelcontextprotocol.io/posts/2025-09-08-mcp-registry-preview/)
- [MCP 思考](https://www.reillywood.com/blog/apis-dont-make-good-mcp-tools/)

## 📝 本周作业

### AI IDE 初体验

在本周的作业中，你将探索 AI IDE 的功能，并尝试使用编码代理完成编程任务。

**作业链接**：[First Steps in the AI IDE](https://github.com/mihail911/modern-software-dev-assignments/tree/master/week2)

## 📅 课程安排

| 日期 | 主题 | 资源 |
|------|------|------|
| 周一 9/29 | 从零开始构建编码代理 | [幻灯片](https://docs.google.com/presentation/d/11CP26VhsjnZOmi9YFgLlonzdib9BLyAlgc4cEvC5Fps/edit?usp=sharing) · [完成示例](https://drive.google.com/file/d/1YtpKFVG13DHyQ2i3HOtwyVJOV90nWeL2/view?usp=drive_link) |
| 周五 10/3 | 构建自定义 MCP 服务器 | [幻灯片](https://docs.google.com/presentation/d/1zSC2ra77XOUrJeyS85houg1DU7z9hq5Y4ebagTch-5o/edit?usp=drive_link) · [完成示例](https://drive.google.com/file/d/1J6lgZWcxPzpCpjujJSnW1aAkCYF6Yxv3/view?usp=drive_link) |

---

**下一步**：开始学习 [代理架构与组件](01-agent-architecture.md)
