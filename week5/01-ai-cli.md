# AI 增强的命令行界面

## 传统终端的局限

传统终端虽然强大，但存在一些限制：

| 局限 | 影响 |
|------|------|
| 需要记忆复杂命令 | 学习曲线陡峭 |
| 错误信息晦涩 | 难以调试 |
| 缺少智能提示 | 效率低下 |
| 无法理解意图 | 需要精确输入 |

## AI 增强的终端特性

### 1. 自然语言命令

```bash
# 传统方式
git log --oneline --graph --all --decorate

# AI 增强方式
> show me the git history as a graph
```

### 2. 智能错误诊断

```bash
$ npm install
npm ERR! code ERESOLVE

# AI 自动分析并建议解决方案
> This error occurs because of a dependency conflict.
> Try running: npm install --legacy-peer-deps
```

### 3. 命令建议

```bash
$ git cmmit
> Did you mean 'git commit'? [Y/n]
```

### 4. 上下文感知

```bash
# 终端记住项目上下文
$ run tests
> Running pytest for project 'myapp'...
```

## Warp 简介

Warp 是一个现代的、AI 增强的终端。

### 核心功能

| 功能 | 描述 |
|------|------|
| **AI 命令搜索** | 用自然语言搜索命令 |
| **智能错误修复** | 自动诊断和修复错误 |
| **协作功能** | 团队共享命令和工作流 |
| **可视化输出** | 更好的输出展示 |
| **块编辑** | 像编辑文本一样编辑命令 |

### Warp 基本使用

```bash
# 安装 Warp
# https://warp.dev/download

# 启动 Warp
warp

# 使用 AI 搜索
# 按 Ctrl+P，输入自然语言描述
> how to kill a process on port 3000

# Warp 会建议命令
kill -9 $(lsof -t -i:3000)
```

## AI CLI 模式

### 模式 1：命令生成

```bash
# 用户描述需求
> create a new Python project with FastAPI

# AI 生成命令序列
mkdir myproject && cd myproject
python -m venv venv
source venv/bin/activate
pip install fastapi uvicorn
touch main.py
```

### 模式 2：命令解释

```bash
# 用户询问命令含义
> explain: find . -name "*.py" -type f -exec grep "TODO" {} +

# AI 解释
# 在当前目录及其子目录中查找所有 .py 文件，
# 并在这些文件中搜索包含 "TODO" 的行
```

### 模式 3：命令优化

```bash
# 用户输入命令
cat file.txt | grep "error" | wc -l

# AI 建议优化
grep -c "error" file.txt
# 更高效，不需要管道
```

## 终端自动化

### 1. Shell 脚本

```bash
#!/bin/bash
# 自动化部署脚本

# 更新代码
git pull origin main

# 安装依赖
npm install

# 运行测试
npm test

# 如果测试通过，部署
if [ $? -eq 0 ]; then
    npm run deploy
else
    echo "Tests failed, deployment aborted"
fi
```

### 2. AI 辅助脚本生成

```bash
# 用户描述需求
> create a script to backup my database daily

# AI 生成脚本
#!/bin/bash
# Daily database backup script

DATE=$(date +%Y%m%d)
BACKUP_DIR="/backups"
DB_NAME="mydb"

pg_dump $DB_NAME > $BACKUP_DIR/$DB_NAME_$DATE.sql

# Keep only last 7 days
find $BACKUP_DIR -name "*.sql" -mtime +7 -delete
```

### 3. 工作流自动化

```bash
# 使用 AI 创建工作流
> create a workflow for: test, build, deploy

# AI 生成 Makefile
.PHONY: test build deploy

test:
    npm test

build:
    npm run build

deploy: test build
    npm run deploy
```

## 实用技巧

### 1. 命令历史搜索

```bash
# 使用 AI 搜索历史
> find command that installed webpack
# AI 找到: npm install webpack --save-dev
```

### 2. 错误快速修复

```bash
# AI 自动修复常见错误
$ python script.py
ModuleNotFoundError: No module named 'requests'

> AI suggests: pip install requests
```

### 3. 项目上下文

```bash
# Warp 记住项目信息
$ run server
> Starting server for project 'myapp' on port 8000...
```

## 下一步

现在你已经了解了 AI CLI，让我们学习终端自动化：

→ [终端自动化与脚本](02-terminal-automation.md)
