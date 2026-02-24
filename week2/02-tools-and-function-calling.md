# 工具使用与函数调用

## 什么是工具使用？

工具使用（Tool Use）是指让 LLM 能够调用外部函数或 API 来执行特定任务的能力。这使得 LLM 不仅仅是生成文本，而是能够与外部世界进行交互。

## 为什么需要工具？

LLM 本身的能力是有限的：

| LLM 的限制 | 工具的解决方案 |
|-----------|---------------|
| 无法访问实时信息 | 调用搜索 API |
| 无法执行代码 | 运行代码解释器 |
| 无法访问文件系统 | 使用文件操作工具 |
| 无法进行数学计算 | 调用计算器函数 |
| 无法访问外部服务 | 通过 API 交互 |

## 函数调用的工作原理

### 1. 定义工具

首先，向 LLM 描述可用的工具：

```python
tools = [
    {
        "name": "read_file",
        "description": "读取文件内容",
        "parameters": {
            "type": "object",
            "properties": {
                "path": {
                    "type": "string",
                    "description": "文件路径"
                }
            },
            "required": ["path"]
        }
    },
    {
        "name": "write_file",
        "description": "写入文件内容",
        "parameters": {
            "type": "object",
            "properties": {
                "path": {
                    "type": "string",
                    "description": "文件路径"
                },
                "content": {
                    "type": "string",
                    "description": "文件内容"
                }
            },
            "required": ["path", "content"]
        }
    },
    {
        "name": "execute_code",
        "description": "执行 Python 代码",
        "parameters": {
            "type": "object",
            "properties": {
                "code": {
                    "type": "string",
                    "description": "要执行的代码"
                }
            },
            "required": ["code"]
        }
    }
]
```

### 2. LLM 决策调用工具

LLM 根据用户请求和工具描述，决定是否需要调用工具：

```
用户：读取 config.json 文件的内容

LLM 思考：
- 用户需要读取文件
- 我有 read_file 工具
- 需要参数：path = "config.json"

LLM 输出：
{
    "tool": "read_file",
    "parameters": {
        "path": "config.json"
    }
}
```

### 3. 执行工具调用

系统执行 LLM 请求的工具调用：

```python
def execute_tool_call(tool_call):
    tool_name = tool_call["tool"]
    parameters = tool_call["parameters"]

    if tool_name == "read_file":
        return read_file(parameters["path"])
    elif tool_name == "write_file":
        return write_file(parameters["path"], parameters["content"])
    elif tool_name == "execute_code":
        return execute_code(parameters["code"])
    else:
        return f"未知工具: {tool_name}"
```

### 4. 将结果反馈给 LLM

将工具执行结果返回给 LLM，让它继续处理：

```python
# 工具执行结果
tool_result = {
    "tool": "read_file",
    "result": "{\n  \"port\": 8080,\n  \"debug\": true\n}"
}

# 将结果添加到对话历史
conversation.append({
    "role": "tool",
    "name": "read_file",
    "content": tool_result["result"]
})

# LLM 继续处理
response = llm.generate(conversation)
```

## 常用工具类型

### 1. 文件系统工具

```python
def read_file(path: str) -> str:
    """读取文件内容"""
    with open(path, 'r') as f:
        return f.read()

def write_file(path: str, content: str) -> str:
    """写入文件内容"""
    with open(path, 'w') as f:
        f.write(content)
    return f"文件已写入: {path}"

def list_files(directory: str) -> list:
    """列出目录中的文件"""
    return os.listdir(directory)

def search_files(pattern: str, directory: str) -> list:
    """搜索匹配模式的文件"""
    results = []
    for root, dirs, files in os.walk(directory):
        for file in files:
            if pattern in file:
                results.append(os.path.join(root, file))
    return results
```

### 2. 代码执行工具

```python
def execute_python(code: str) -> dict:
    """执行 Python 代码"""
    try:
        exec_globals = {}
        exec(code, exec_globals)
        return {
            "success": True,
            "output": str(exec_globals.get('output', ''))
        }
    except Exception as e:
        return {
            "success": False,
            "error": str(e)
        }

def run_tests(test_path: str) -> dict:
    """运行测试"""
    import subprocess
    result = subprocess.run(
        ['python', '-m', 'pytest', test_path],
        capture_output=True,
        text=True
    )
    return {
        "returncode": result.returncode,
        "stdout": result.stdout,
        "stderr": result.stderr
    }
```

### 3. 搜索工具

```python
def search_code(query: str, directory: str) -> list:
    """在代码中搜索"""
    results = []
    for root, dirs, files in os.walk(directory):
        for file in files:
            if file.endswith('.py'):
                path = os.path.join(root, file)
                with open(path, 'r') as f:
                    content = f.read()
                    if query in content:
                        results.append({
                            "file": path,
                            "matches": content.count(query)
                        })
    return results

def search_web(query: str) -> list:
    """网络搜索"""
    # 使用搜索 API
    # 返回搜索结果
    pass
```

### 4. Git 工具

```python
def git_diff(file: str = None) -> str:
    """查看 Git 差异"""
    import subprocess
    cmd = ['git', 'diff']
    if file:
        cmd.append(file)
    result = subprocess.run(cmd, capture_output=True, text=True)
    return result.stdout

def git_commit(message: str) -> str:
    """提交更改"""
    import subprocess
    subprocess.run(['git', 'add', '.'])
    result = subprocess.run(
        ['git', 'commit', '-m', message],
        capture_output=True,
        text=True
    )
    return result.stdout
```

## 实际应用示例

### 示例 1：代码重构

```
用户：重构 utils.py 中的所有函数，添加类型提示

代理执行流程：
1. 调用 read_file("utils.py") 读取文件
2. LLM 分析代码，生成重构版本
3. 调用 write_file("utils.py", new_code) 写入新代码
4. 调用 run_tests() 验证没有破坏功能
5. 向用户报告结果
```

### 示例 2：调试

```
用户：帮我调试这个错误：TypeError: 'NoneType' object is not iterable

代理执行流程：
1. 调用 search_code("TypeError") 搜索相关代码
2. 调用 read_file() 读取相关文件
3. LLM 分析代码，识别问题
4. 调用 execute_code() 验证修复方案
5. 调用 write_file() 应用修复
```

### 示例 3：添加新功能

```
用户：为 User 模型添加一个 get_age() 方法

代理执行流程：
1. 调用 search_code("class User") 找到 User 模型
2. 调用 read_file() 读取文件
3. LLM 生成新方法
4. 调用 write_file() 添加方法
5. 调用 run_tests() 确保测试通过
```

## 工具设计最佳实践

### ✅ DO

| 实践 | 说明 |
|------|------|
| 清晰的描述 | 详细说明工具的功能和用途 |
| 明确的参数 | 指定参数类型和是否必需 |
| 错误处理 | 提供清晰的错误信息 |
| 幂等性 | 相同输入产生相同输出 |
| 安全性 | 验证输入，防止注入攻击 |

### ❌ DON'T

| 实践 | 说明 |
|------|------|
| 模糊的描述 | 导致 LLM 误用工具 |
| 缺少验证 | 可能导致安全问题 |
| 过于复杂 | 增加 LLM 理解难度 |
| 副作用 | 难以调试和恢复 |

## 工具调用优化

### 1. 批量调用

```python
# 串行调用（慢）
for file in files:
    read_file(file)

# 批量调用（快）
batch_read_files(files)
```

### 2. 并行调用

```python
import concurrent.futures

def parallel_tool_calls(calls):
    with concurrent.futures.ThreadPoolExecutor() as executor:
        futures = [executor.submit(execute_tool_call, call) for call in calls]
        return [f.result() for f in futures]
```

### 3. 缓存结果

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def read_file_cached(path: str) -> str:
    return read_file(path)
```

## 下一步

现在你已经了解了工具使用，让我们学习模型上下文协议（MCP）：

→ [模型上下文协议 (MCP)](03-mcp.md)
