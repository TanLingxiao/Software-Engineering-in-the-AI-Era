# IDE 集成与扩展

## IDE 集成概述

AI 功能与 IDE 的集成是现代软件开发的关键。良好的集成可以让 AI 辅助开发无缝融入开发者的工作流程。

## 主流 AI IDE

| IDE | 特点 | 集成方式 |
|-----|------|---------|
| **Cursor** | 基于 VS Code，深度 AI 集成 | 原生支持 |
| **Claude Code** | Anthropic 官方，强大的代码理解 | 原生支持 |
| **Warp** | AI 增强的终端 | 命令行集成 |
| **VS Code + Copilot** | 微软官方，广泛使用 | 扩展插件 |
| **JetBrains AI** | JetBrains 全家桶支持 | 原生支持 |

## Cursor 集成

### 安装和设置

```bash
# 下载 Cursor
# https://cursor.sh/download

# 安装后，配置 API 密钥
# Settings → AI Provider → 选择提供商
```

### 核心功能

#### 1. AI Chat

```python
# 使用 AI Chat
# Cmd/Ctrl + L 打开 AI Chat

# 示例对话
用户：帮我解释这段代码
@src/utils/helpers.py

# Cursor 会自动包含文件内容并解释
```

#### 2. AI Edit

```python
# 使用 AI Edit
# 选中代码 → Cmd/Ctrl + K

# 示例
# 选中函数 → Cmd/Ctrl + K
# 输入：添加类型提示和文档字符串

# Cursor 会修改代码
```

#### 3. AI Generate

```python
# 使用 AI Generate
# Cmd/Ctrl + I

# 示例
# 输入：创建一个 REST API 端点来处理用户注册

# Cursor 会生成完整的代码
```

### Cursor 扩展

```json
// .cursorrules
{
  "rules": [
    "使用 TypeScript 严格模式",
    "所有函数必须有类型提示",
    "使用 async/await 而不是 Promise",
    "所有 API 端点必须有错误处理"
  ]
}
```

## Claude Code 集成

### 安装和设置

```bash
# 安装 Claude Code CLI
npm install -g @anthropic-ai/claude-code

# 配置 API 密钥
claude auth login
```

### 核心功能

#### 1. 代码分析

```bash
# 分析代码
claude analyze src/

# 查找问题
claude find-issues src/
```

#### 2. 代码生成

```bash
# 生成代码
claude generate "创建一个用户认证系统"

# 基于模板生成
claude generate --template rest-api
```

#### 3. 代码审查

```bash
# 审查代码
claude review src/

# 审查特定文件
claude review src/models/user.py
```

### Claude Code API

```python
from anthropic import Anthropic

client = Anthropic(api_key="your-api-key")

# 代码审查
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "审查这段代码：\n" + code
    }]
)
```

## VS Code 扩展开发

### 创建 AI 扩展

```typescript
// extension.ts
import * as vscode from 'vscode';

export function activate(context: vscode.ExtensionContext) {
    // 注册命令
    const disposable = vscode.commands.registerCommand(
        'myai.generateCode',
        async () => {
            const editor = vscode.window.activeTextEditor;
            const selection = editor?.selection;
            const text = editor?.document.getText(selection);
            
            // 调用 AI API
            const result = await callAI(text);
            
            // 插入结果
            editor?.edit(editBuilder => {
                editBuilder.insert(selection.end, '\n' + result);
            });
        }
    );
    
    context.subscriptions.push(disposable);
}

async function callAI(prompt: string): Promise<string> {
    // 调用 AI API
    return "生成的代码";
}
```

### package.json 配置

```json
{
  "name": "my-ai-extension",
  "displayName": "My AI Extension",
  "version": "1.0.0",
  "engines": {
    "vscode": "^1.80.0"
  },
  "categories": ["Other"],
  "activationEvents": [
    "onCommand:myai.generateCode"
  ],
  "contributes": {
    "commands": [
      {
        "command": "myai.generateCode",
        "title": "Generate Code with AI"
      }
    ],
    "keybindings": [
      {
        "command": "myai.generateCode",
        "key": "ctrl+shift+g"
      }
    ]
  }
}
```

## MCP 服务器集成

### 在 IDE 中使用 MCP

```typescript
// VS Code 扩展中集成 MCP
import { MCPClient } from '@modelcontextprotocol/sdk/client';

const client = new MCPClient({
  serverUrl: 'stdio://my-mcp-server'
});

// 调用 MCP 工具
const result = await client.callTool({
  name: 'read_file',
  arguments: { path: 'src/main.py' }
});
```

### MCP 服务器示例

```typescript
// 文件系统 MCP 服务器
import { Server } from '@modelcontextprotocol/sdk/server';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio';

const server = new Server({
  name: 'filesystem-server',
  version: '1.0.0'
});

server.setRequestHandler('tools/call', async (request) => {
  const { name, arguments: args } = request.params;
  
  if (name === 'read_file') {
    const content = await fs.readFile(args.path, 'utf-8');
    return {
      content: [{ type: 'text', text: content }]
    };
  }
  
  throw new Error(`Unknown tool: ${name}`);
});

await server.connect(new StdioServerTransport());
```

## 集成最佳实践

### 1. 上下文感知

```typescript
// 根据当前文件类型调整 AI 行为
const fileType = vscode.window.activeTextEditor?.document.languageId;

let prompt = '';
switch (fileType) {
  case 'python':
    prompt = '使用 Python 最佳实践';
    break;
  case 'typescript':
    prompt = '使用 TypeScript 严格模式';
    break;
  case 'javascript':
    prompt = '使用 ES6+ 语法';
    break;
}
```

### 2. 渐进式披露

```typescript
// 先显示摘要，用户可以展开查看详情
const summary = await getSummary(code);
vscode.window.showInformationMessage(summary);

// 用户点击查看详情
vscode.window.showQuickPick(['查看详情'], {
  onDidSelectItem: async () => {
    const details = await getDetails(code);
    vscode.window.showTextDocument(
      vscode.Uri.parse('untitled:details'),
      { content: details }
    );
  }
});
```

### 3. 错误处理

```typescript
try {
  const result = await callAI(prompt);
  showResult(result);
} catch (error) {
  if (error instanceof APIError) {
    vscode.window.showErrorMessage(
      `API 错误: ${error.message}`
    );
  } else if (error instanceof NetworkError) {
    vscode.window.showWarningMessage(
      '网络错误，请检查连接'
    );
  } else {
    vscode.window.showErrorMessage(
      '未知错误'
    );
  }
}
```

## 下一步

现在你已经了解了 IDE 集成，让我们完成本周的作业：

→ [作业：构建自定义 MCP 服务器](04-assignment.md)
