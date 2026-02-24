# 代理的产品需求文档 (PRD)

## 什么是代理 PRD？

代理 PRD（Product Requirements Document）是为编码代理编写的产品需求文档。与传统 PRD 不同，代理 PRD 需要更加详细和结构化，以便 AI 能够准确理解和执行。

## 为什么需要代理 PRD？

| 传统 PRD | 代理 PRD |
|---------|---------|
| 面向人类开发者 | 面向 AI 代理 |
| 可以模糊和灵活 | 必须明确和精确 |
| 依赖上下文理解 | 需要显式说明 |
| 可以迭代澄清 | 需要一次性完整 |

## 代理 PRD 的结构

### 1. 项目概述

```markdown
# 项目概述

## 项目名称
[项目名称]

## 项目目标
[清晰、简洁的目标描述]

## 背景
[为什么需要这个项目]

## 成功标准
[如何判断项目是否成功]
```

### 2. 技术栈

```markdown
# 技术栈

## 后端
- 语言：[Python/Node.js/等]
- 框架：[Flask/FastAPI/Express/等]
- 数据库：[PostgreSQL/MongoDB/等]

## 前端
- 框架：[React/Vue/等]
- UI 库：[Material-UI/Tailwind/等]

## 工具
- 包管理器：[npm/pip/等]
- 版本控制：[Git]
- 测试框架：[pytest/Jest/等]
```

### 3. 功能需求

```markdown
# 功能需求

## 核心功能

### 功能 1：[功能名称]
**描述**：[详细描述]
**输入**：[输入格式]
**输出**：[输出格式]
**依赖**：[依赖的其他功能]
**优先级**：[高/中/低]

### 功能 2：[功能名称]
...
```

### 4. 数据模型

```markdown
# 数据模型

## User 模型
```typescript
interface User {
  id: string;
  username: string;
  email: string;
  password_hash: string;
  created_at: Date;
  updated_at: Date;
}
```

## Post 模型
...
```

### 5. API 规范

```markdown
# API 规范

## POST /api/users
**描述**：创建新用户
**请求体**：
```json
{
  "username": "string",
  "email": "string",
  "password": "string"
}
```
**响应**：
```json
{
  "id": "string",
  "username": "string",
  "email": "string"
}
```
**状态码**：201 Created
```

### 6. 约束条件

```markdown
# 约束条件

## 性能要求
- API 响应时间 < 200ms
- 支持并发 1000 用户

## 安全要求
- 密码必须加密存储
- API 必须使用 HTTPS
- 实现速率限制

## 兼容性要求
- 支持 Chrome 90+
- 支持 Safari 14+
```

### 7. 测试要求

```markdown
# 测试要求

## 单元测试
- 覆盖率 > 80%
- 所有核心功能必须有测试

## 集成测试
- 测试 API 端点
- 测试数据库操作

## 端到端测试
- 测试用户流程
- 测试错误处理
```

## 编写代理 PRD 的最佳实践

### 1. 使用结构化格式

```markdown
❌ 不好的做法：
创建一个用户系统，可以注册、登录、修改密码。

✅ 好的做法：
## 用户认证系统

### 1.1 用户注册
**端点**：POST /api/auth/register
**请求体**：
```json
{
  "username": "string (3-20 字符)",
  "email": "string (有效邮箱格式)",
  "password": "string (至少 8 字符，包含大小写字母和数字)"
}
```
**验证规则**：
- username: 3-20 字符，只允许字母、数字、下划线
- email: 必须是有效邮箱格式
- password: 至少 8 字符，包含大小写字母和数字
**响应**：
- 成功：201 Created，返回用户信息（不含密码）
- 失败：400 Bad Request，返回错误信息
```

### 2. 提供示例

```markdown
### 示例请求
```bash
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john_doe",
    "email": "john@example.com",
    "password": "SecurePass123"
  }'
```

### 示例响应
```json
{
  "id": "123e4567-e89b-12d3-a456-426614174000",
  "username": "john_doe",
  "email": "john@example.com",
  "created_at": "2024-01-15T10:30:00Z"
}
```
```

### 3. 明确错误处理

```markdown
### 错误响应

#### 400 Bad Request
```json
{
  "error": "validation_error",
  "message": "用户名已被占用",
  "details": {
    "field": "username",
    "value": "john_doe"
  }
}
```

#### 401 Unauthorized
```json
{
  "error": "authentication_error",
  "message": "无效的凭据"
}
```

#### 500 Internal Server Error
```json
{
  "error": "server_error",
  "message": "内部服务器错误"
}
```
```

### 4. 定义清晰的边界

```markdown
### 范围内
- 用户注册和登录
- 基本的 CRUD 操作
- 简单的搜索功能

### 范围外
- 社交功能（关注、点赞等）
- 实时通知
- 支付功能
```

## 代理 PRD 模板

```markdown
# [项目名称] - 产品需求文档

## 1. 项目概述
### 1.1 项目目标
### 1.2 项目背景
### 1.3 成功标准

## 2. 技术栈
### 2.1 后端技术
### 2.2 前端技术
### 2.3 数据库
### 2.4 工具和库

## 3. 数据模型
### 3.1 核心模型
### 3.2 关系定义

## 4. 功能需求
### 4.1 核心功能
### 4.2 次要功能
### 4.3 未来功能

## 5. API 规范
### 5.1 认证端点
### 5.2 资源端点
### 5.3 错误处理

## 6. 约束条件
### 6.1 性能要求
### 6.2 安全要求
### 6.3 兼容性要求

## 7. 测试要求
### 7.1 单元测试
### 7.2 集成测试
### 7.3 端到端测试

## 8. 部署要求
### 8.1 环境配置
### 8.2 CI/CD 流程
### 8.3 监控和日志

## 9. 附录
### 9.1 术语表
### 9.2 参考资料
```

## 实际应用示例

### 示例：待办事项应用 PRD

```markdown
# 待办事项应用 - 产品需求文档

## 1. 项目概述
### 1.1 项目目标
构建一个简单但功能完整的待办事项应用，允许用户创建、查看、更新和删除待办事项。

### 1.2 成功标准
- 用户可以成功注册和登录
- 用户可以创建、查看、更新、删除待办事项
- 数据持久化到数据库
- 所有 API 端点有测试覆盖

## 2. 技术栈
### 2.1 后端
- 语言：Python 3.11+
- 框架：FastAPI
- 数据库：SQLite（开发）/ PostgreSQL（生产）
- ORM：SQLAlchemy

### 2.2 前端
- 框架：React 18
- UI 库：Tailwind CSS
- 状态管理：React Context API

## 3. 数据模型
### 3.1 User 模型
```python
class User(Base):
    __tablename__ = "users"
    
    id = Column(String, primary_key=True)
    username = Column(String, unique=True, nullable=False)
    email = Column(String, unique=True, nullable=False)
    password_hash = Column(String, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
```

### 3.2 Todo 模型
```python
class Todo(Base):
    __tablename__ = "todos"
    
    id = Column(String, primary_key=True)
    user_id = Column(String, ForeignKey("users.id"))
    title = Column(String, nullable=False)
    description = Column(String)
    completed = Column(Boolean, default=False)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
```

## 4. API 规范
### 4.1 认证端点

#### POST /api/auth/register
**请求体**：
```json
{
  "username": "string (3-20 字符)",
  "email": "string (有效邮箱)",
  "password": "string (至少 8 字符)"
}
```
**响应**：201 Created

#### POST /api/auth/login
**请求体**：
```json
{
  "email": "string",
  "password": "string"
}
```
**响应**：200 OK，返回 JWT token

### 4.2 Todo 端点

#### POST /api/todos
**请求体**：
```json
{
  "title": "string",
  "description": "string (可选)"
}
```
**响应**：201 Created

#### GET /api/todos
**查询参数**：
- completed: boolean (可选)
- limit: integer (默认 10)
- offset: integer (默认 0)

**响应**：200 OK，返回待办事项列表

#### PUT /api/todos/{id}
**请求体**：
```json
{
  "title": "string (可选)",
  "description": "string (可选)",
  "completed": "boolean (可选)"
}
```
**响应**：200 OK

#### DELETE /api/todos/{id}
**响应**：204 No Content
```

## 下一步

现在你已经了解了如何为代理编写 PRD，让我们学习 IDE 集成：

→ [IDE 集成与扩展](03-ide-integrations.md)
