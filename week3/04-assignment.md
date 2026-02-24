# 作业：构建自定义 MCP 服务器

## 📋 作业概述

本作业将让你构建一个自定义的 MCP 服务器，将特定的数据源或功能集成到 AI IDE 中。

## 🎯 学习目标

完成本作业后，你将能够：

- 理解 MCP 服务器的工作原理
- 使用 MCP SDK 创建服务器
- 定义资源和工具
- 在 AI IDE 中集成你的 MCP 服务器

## 📝 作业任务

### 任务 1：选择 MCP 服务器类型

从以下选项中选择一个来实现：

**选项 A：文件系统 MCP 服务器**
- 读取、写入、列出文件
- 搜索文件内容
- 监控文件变化

**选项 B：数据库 MCP 服务器**
- 连接到 SQLite/PostgreSQL
- 执行 SQL 查询
- 列出表和结构

**选项 C：API MCP 服务器**
- 调用外部 API
- 处理认证
- 缓存响应

**选项 D：自定义数据源**
- 选择你感兴趣的数据源
- 实现 MCP 接口
- 提供相关工具

### 任务 2：设计 MCP 服务器

设计你的 MCP 服务器：

```
服务器名称：[名称]
版本：1.0.0
描述：[描述]

资源：
- 资源 1：[名称] - [描述]
- 资源 2：[名称] - [描述]

工具：
- 工具 1：[名称] - [描述]
  - 输入：[参数]
  - 输出：[返回值]
- 工具 2：[名称] - [描述]
  - 输入：[参数]
  - 输出：[返回值]
```

### 任务 3：实现 MCP 服务器

使用 TypeScript 或 Python SDK 实现你的 MCP 服务器。

**TypeScript 模板**：

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server({
  name: "my-mcp-server",
  version: "1.0.0"
});

// 实现工具列表
server.setRequestHandler("tools/list", async () => ({
  tools: [
    {
      name: "my_tool",
      description: "工具描述",
      inputSchema: {
        type: "object",
        properties: {
          param: { type: "string" }
        },
        required: ["param"]
      }
    }
  ]
}));

// 实现工具调用
server.setRequestHandler("tools/call", async (request) => {
  const { name, arguments: args } = request.params;
  
  if (name === "my_tool") {
    // 实现工具逻辑
    const result = await myToolFunction(args.param);
    return {
      content: [{ type: "text", text: result }]
    };
  }
  
  throw new Error(`Unknown tool: ${name}`);
});

await server.connect(new StdioServerTransport());
```

### 任务 4：测试 MCP 服务器

测试你的 MCP 服务器：

```bash
# 启动服务器
node dist/index.js

# 测试工具调用
echo '{"jsonrpc":"2.0","method":"tools/list","id":1}' | node dist/index.js

echo '{"jsonrpc":"2.0","method":"tools/call","params":{"name":"my_tool","arguments":{"param":"test"}},"id":2}' | node dist/index.js
```

### 任务 5：在 AI IDE 中集成

将你的 MCP 服务器集成到 AI IDE 中：

**Cursor 配置**：

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["/path/to/dist/index.js"]
    }
  }
}
```

**Claude Code 配置**：

```json
{
  "mcpServers": [
    {
      "name": "my-server",
      "command": "node",
      "args": ["/path/to/dist/index.js"]
    }
  ]
}
```

### 任务 6：文档和示例

为你的 MCP 服务器编写文档：

```markdown
# [服务器名称] MCP 服务器

## 描述
[详细描述]

## 安装
[安装步骤]

## 配置
[配置说明]

## 工具列表

### tool_name
**描述**：[描述]
**参数**：
- param1: [类型] - [描述]
- param2: [类型] - [描述]

**示例**：
```json
{
  "param1": "value1",
  "param2": "value2"
}
```

## 使用示例
[使用示例]
```

## 📊 提交要求

提交以下内容：

1. **源代码**
   - 完整的 MCP 服务器实现
   - package.json 或 requirements.txt
   - README.md

2. **文档**
   - 服务器设计文档
   - 使用指南
   - API 文档

3. **测试**
   - 测试用例
   - 测试结果

4. **集成示例**
   - IDE 配置示例
   - 使用演示

## 💡 提示

- 从简单的工具开始，逐步添加功能
- 使用 TypeScript 类型确保类型安全
- 添加详细的错误处理
- 编写清晰的文档
- 考虑安全性（输入验证、认证等）

## 📚 参考资料

- [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [MCP 示例服务器](https://github.com/modelcontextprotocol/servers)

## ⏰ 截止日期

请在本周五之前完成作业并提交。

---

**下一步**：完成作业后，继续学习 [第4周：编码代理模式](../week4/README.md)
