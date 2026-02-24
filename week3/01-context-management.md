# 上下文管理与代码理解

## 什么是上下文管理？

上下文管理是指在 AI 辅助开发中，如何有效地向 LLM 提供相关的代码和信息，使其能够准确理解和完成任务。

## 上下文的重要性

```
上下文质量 → LLM 理解能力 → 代码质量
```

| 上下文质量 | LLM 表现 | 结果 |
|-----------|---------|------|
| **高质量** | 准确理解需求 | 生成高质量代码 |
| **中等质量** | 部分理解 | 需要多轮交互 |
| **低质量** | 误解需求 | 生成错误代码 |

## 上下文窗口限制

### 什么是上下文窗口？

上下文窗口是 LLM 能够处理的最大输入长度，通常以 token 数量衡量。

| 模型 | 上下文窗口 |
|------|-----------|
| GPT-3.5 | 16K tokens |
| GPT-4 | 128K tokens |
| Claude 3 | 200K tokens |
| Claude 3.5 | 200K tokens |

### 上下文窗口的挑战

```
问题：代码库太大，无法全部放入上下文

解决方案：
1. 选择性包含相关代码
2. 使用摘要和压缩
3. 分块处理
4. 使用检索增强生成 (RAG)
```

## 上下文管理策略

### 1. 选择性包含

只包含与任务相关的代码：

```python
# ❌ 不好的做法：包含整个文件
def process_data(data):
    # ... 100 行代码 ...
    return result

def validate_data(data):
    # ... 50 行代码 ...
    return is_valid

def save_data(data):
    # ... 30 行代码 ...
    return success

# 用户只想修改 process_data 函数
```

```python
# ✅ 好的做法：只包含相关函数
def process_data(data):
    # ... 100 行代码 ...
    return result

# 用户只关心 process_data 函数
```

### 2. 代码摘要

对不相关但需要了解的代码进行摘要：

```
完整代码（200 行）：
[大量代码]

摘要（5 行）：
- process_data() 函数处理输入数据
- 使用 validate_data() 验证数据
- 调用 save_data() 保存结果
- 返回处理后的数据
```

### 3. 分层上下文

使用分层结构组织上下文：

```
第1层：核心代码（直接相关）
- 正在修改的函数
- 直接调用的函数

第2层：相关代码（间接相关）
- 相关的数据结构
- 相关的配置

第3层：上下文信息（背景）
- 项目结构
- 设计文档摘要
```

### 4. 动态上下文

根据任务动态调整上下文：

```python
def get_context(task, codebase):
    context = {
        "core": get_core_code(task, codebase),
        "related": get_related_code(task, codebase),
        "docs": get_relevant_docs(task, codebase)
    }
    return optimize_context(context)
```

## 代码理解技术

### 1. 代码索引

建立代码索引以便快速检索：

```python
class CodeIndex:
    def __init__(self):
        self.functions = {}
        self.classes = {}
        self.variables = {}
    
    def index_file(self, file_path):
        code = read_file(file_path)
        self._extract_functions(code)
        self._extract_classes(code)
        self._extract_variables(code)
    
    def find_related(self, query):
        # 查找相关的代码
        pass
```

### 2. 依赖分析

分析代码之间的依赖关系：

```
A.py
├── imports B.py
│   └── imports C.py
└── imports D.py

依赖树：
A → B → C
A → D
```

### 3. 语义搜索

使用向量搜索找到语义相似的代码：

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')

def search_similar_code(query, code_snippets):
    query_embedding = model.encode(query)
    code_embeddings = model.encode(code_snippets)
    similarities = cosine_similarity(query_embedding, code_embeddings)
    return sorted(zip(code_snippets, similarities), key=lambda x: -x[1])
```

## 上下文工程最佳实践

### ✅ DO

| 实践 | 说明 |
|------|------|
| 明确任务 | 清楚说明要做什么 |
| 提供示例 | 展示期望的输入输出 |
| 渐进式包含 | 从核心代码开始，逐步添加 |
| 使用注释 | 解释代码的意图 |
| 保持简洁 | 只包含必要信息 |

### ❌ DON'T

| 实践 | 说明 |
|------|------|
| 包含无关代码 | 浪费上下文空间 |
| 重复信息 | 导致混淆 |
| 过早优化 | 先确保正确性 |
| 忽略错误信息 | 错误信息很重要 |
| 缺少上下文 | LLM 无法理解 |

## 实际应用示例

### 示例 1：修复 bug

```
用户：修复 login 函数中的 bug

上下文应该包含：
1. login 函数的完整代码
2. 相关的验证函数
3. 错误信息
4. 相关的测试

不应该包含：
1. 整个项目的所有代码
2. 不相关的功能
3. 注释掉的代码
```

### 示例 2：添加新功能

```
用户：为 User 模型添加 email 验证

上下文应该包含：
1. User 模型的定义
2. 相关的验证逻辑
3. 数据库模式
4. 现有的验证示例

不应该包含：
1. 其他模型
2. 前端代码
3. 配置文件
```

### 示例 3：重构代码

```
用户：重构 utils.py 中的所有函数

上下文应该包含：
1. utils.py 的完整内容
2. 使用这些函数的代码示例
3. 相关的测试
4. 性能要求

不应该包含：
1. 不使用这些函数的代码
2. 不相关的工具函数
3. 文档字符串（可以摘要）
```

## 上下文管理工具

### 1. Cursor 的上下文管理

```python
# Cursor 自动管理上下文
# - @符号引用文件
# - 智能选择相关代码
# - 上下文压缩

# 示例
@src/models/user.py
@src/utils/validation.py

# 添加新字段
```

### 2. Claude Code 的上下文管理

```python
# Claude Code 的上下文管理
# - 自动分析代码依赖
# - 智能上下文建议
# - 上下文优先级

# 示例
/analyze src/models/user.py
```

### 3. Warp 的上下文管理

```bash
# Warp 的上下文管理
# - 命令历史上下文
# - 文件系统上下文
# - 项目结构上下文

# 示例
# 在项目目录中，Warp 自动包含项目结构
```

## 上下文优化技巧

### 1. 压缩技术

```python
def compress_code(code):
    # 移除注释
    code = remove_comments(code)
    # 移除空行
    code = remove_blank_lines(code)
    # 简化变量名（临时）
    code = simplify_variables(code)
    return code
```

### 2. 摘要技术

```python
def summarize_code(code):
    # 提取函数签名
    functions = extract_functions(code)
    # 提取类定义
    classes = extract_classes(code)
    # 生成摘要
    summary = generate_summary(functions, classes)
    return summary
```

### 3. 分块技术

```python
def chunk_code(code, max_tokens):
    # 按函数分块
    chunks = split_by_functions(code)
    # 如果太大，进一步分块
    for chunk in chunks:
        if count_tokens(chunk) > max_tokens:
            chunk = split_chunk(chunk, max_tokens)
    return chunks
```

## 下一步

现在你已经了解了上下文管理，让我们学习如何为代理编写 PRD：

→ [代理的产品需求文档 (PRD)](02-prds-for-agents.md)
