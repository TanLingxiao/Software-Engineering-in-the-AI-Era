# 模型上下文协议 (MCP)

## 什么是 MCP？

模型上下文协议（Model Context Protocol，简称 MCP）是一个开放标准，用于在 AI 应用和外部数据源/工具之间建立标准化的连接方式。它让 LLM 能够安全、高效地访问外部资源。

## MCP 的核心概念

### 1. 标准化接口

MCP 定义了一套标准化的接口，使得不同的 AI 应用可以与不同的数据源进行交互：

```
┌─────────────┐         MCP          ┌─────────────┐
│   AI 应用    │ ◄──────────────►  │  MCP 服务器   │
│  (Claude)    │                     │  (数据源)    │
└─────────────┘                     └─────────────┘
       │                                   │
       │                                   │
       └─────────  标准化协议  ────────────┘
```

### 2. 服务器-客户端架构

- **MCP 服务器**：提供数据和工具的接口
- **MCP 客户端**：AI 应用，通过 MCP 协议与服务器通信

## MCP 的优势

| 优势 | 说明 |
|------|------|
| **标准化** | 统一的接口，降低集成成本 |
| **安全性** | 内置认证和授权机制 |
| **可扩展** | 易于添加新的数据源和工具 |
| **互操作性** | 不同 AI 应用可以使用相同的 MCP 服务器 |
| **性能** | 优化的数据传输和缓存机制 |

## MCP 架构

### 基本组件

```
┌─────────────────────────────────────────────────────────┐
│                   MCP 客户端                             │
│  - 协议处理                                              │
│  - 连接管理                                              │
│  - 错误处理                                              │
└────────────────────┬────────────────────────────────────┘
                     │
                     │ MCP 协议
                     │
┌────────────────────▼────────────────────────────────────┐
│                   MCP 服务器                             │
│  ┌───────────────────────────────────────────────────┐  │
│  │              资源 (Resources)                     │  │
│  │  - 文件系统                                        │  │
│  │  - 数据库                                          │  │
│  │  - API 端点                                        │  │
│  └───────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────┐  │
│  │              工具 (Tools)                         │  │
│  │  - 数据查询                                        │  │
│  │  - 文件操作                                        │  │
│  │  - 代码执行                                        │  │
│  └───────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────┐  │
│  │              提示 (Prompts)                       │  │
│  │  - 预定义的提示模板                                │  │
│  │  - 上下文注入                                      │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

## MCP 核心功能

### 1. 资源（Resources）

资源是 MCP 服务器提供的可访问数据。

**资源类型**：

```typescript
// 文件资源
{
  "uri": "file:///path/to/file.txt",
  "name": "配置文件",
  "mimeType": "text/plain"
}

// 数据库资源
{
  "uri": "database://users",
  "name": "用户数据",
  "mimeType": "application/json"
}

// API 资源
{
  "uri": "api://github/repos",
  "name": "GitHub 仓库",
  "mimeType": "application/json"
}
```

**资源操作**：

```typescript
// 读取资源
await client.readResource("file:///path/to/file.txt");

// 列出资源
await client.listResources();

// 订阅资源变更
await client.subscribeToResource("file:///path/to/file.txt");
```

### 2. 工具（Tools）

工具是 MCP 服务器提供的可执行功能。

**工具定义**：

```typescript
{
  "name": "execute_query",
  "description": "执行数据库查询",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {
        "type": "string",
        "description": "SQL 查询语句"
      }
    },
    "required": ["query"]
  }
}
```

**工具调用**：

```typescript
const result = await client.callTool({
  name: "execute_query",
  arguments: {
    query: "SELECT * FROM users WHERE active = true"
  }
});
```

### 3. 提示（Prompts）

提示是预定义的提示模板，可以注入上下文。

**提示定义**：

```typescript
{
  "name": "analyze_code",
  "description": "分析代码质量",
  "arguments": [
    {
      "name": "file_path",
      "description": "要分析的文件路径",
      "required": true
    }
  ]
}
```

**提示使用**：

```typescript
const prompt = await client.getPrompt("analyze_code", {
  file_path: "src/main.py"
});
```

## 创建 MCP 服务器

### 使用 TypeScript SDK

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

// 创建服务器
const server = new Server({
  name: "my-mcp-server",
  version: "1.0.0"
}, {
  capabilities: {
    resources: {},
    tools: {},
    prompts: {}
  }
});

// 注册工具
server.setRequestHandler("tools/list", async () => ({
  tools: [
    {
      name: "read_file",
      description: "读取文件内容",
      inputSchema: {
        type: "object",
        properties: {
          path: { type: "string" }
        },
        required: ["path"]
      }
    }
  ]
}));

// 处理工具调用
server.setRequestHandler("tools/call", async (request) => {
  const { name, arguments: args } = request.params;

  if (name === "read_file") {
    const content = await readFile(args.path);
    return {
      content: [{
        type: "text",
        text: content
      }]
    };
  }

  throw new Error(`Unknown tool: ${name}`);
});

// 启动服务器
const transport = new StdioServerTransport();
await server.connect(transport);
```

### 使用 Python SDK

```python
from mcp.server import Server
from mcp.server.stdio import stdio_server

# 创建服务器
server = Server("my-mcp-server")

@server.list_tools()
async def list_tools() -> list[dict]:
    """列出可用的工具"""
    return [
        {
            "name": "read_file",
            "description": "读取文件内容",
            "inputSchema": {
                "type": "object",
                "properties": {
                    "path": {"type": "string"}
                },
                "required": ["path"]
            }
        }
    ]

@server.call_tool()
async def call_tool(name: str, arguments: dict) -> list[dict]:
    """调用工具"""
    if name == "read_file":
        with open(arguments["path"], "r") as f:
            content = f.read()
        return [
            {
                "type": "text",
                "text": content
            }
        ]
    
    raise ValueError(f"Unknown tool: {name}")

# 启动服务器
async def main():
    async with stdio_server() as (read_stream, write_stream):
        await server.run(
            read_stream,
            write_stream,
            server.create_initialization_options()
        )

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
```

## MCP 认证

### API 密钥认证

```typescript
const transport = new StdioServerTransport({
  env: {
    MCP_API_KEY: "your-api-key"
  }
});
```

### OAuth 2.0

```typescript
const transport = new StdioServerTransport({
  env: {
    MCP_OAUTH_TOKEN: "your-oauth-token"
  }
});
```

### 自定义认证

```typescript
const transport = new StdioServerTransport({
  env: {
    MCP_AUTH_HEADER: "Authorization: Bearer token"
  }
});
```

## MCP 注册表

MCP 注册表是一个集中式的 MCP 服务器目录，方便发现和使用各种 MCP 服务器。

### 常用 MCP 服务器

| 服务器 | 功能 | 安装 |
|--------|------|------|
| **filesystem** | 文件系统访问 | `npm install @modelcontextprotocol/server-filesystem` |
| **github** | GitHub API 集成 | `npm install @modelcontextprotocol/server-github` |
| **postgres** | PostgreSQL 数据库 | `npm install @modelcontextprotocol/server-postgres` |
| **sqlite** | SQLite 数据库 | `npm install @modelcontextprotocol/server-sqlite` |
| **brave-search** | Brave 搜索 | `npm install @modelcontextprotocol/server-brave-search` |

## 最佳实践

### 1. 错误处理

```typescript
try {
  const result = await client.callTool({
    name: "read_file",
    arguments: { path: "nonexistent.txt" }
  });
} catch (error) {
  if (error instanceof ResourceNotFoundError) {
    console.error("文件不存在");
  } else {
    console.error("未知错误:", error);
  }
}
```

### 2. 资源限制

```typescript
const client = new MCPClient({
  maxResourceSize: 10 * 1024 * 1024, // 10MB
  maxToolTimeout: 30000 // 30秒
});
```

### 3. 缓存

```typescript
const client = new MCPClient({
  cache: {
    enabled: true,
    ttl: 60000 // 1分钟
  }
});
```

## 下一步

现在你已经了解了 MCP，让我们完成本周的作业：

→ [作业：AI IDE 初体验](04-assignment.md)
