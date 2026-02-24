# 终端自动化与脚本

## 自动化的重要性

自动化可以：
- 节省时间
- 减少错误
- 提高一致性
- 释放创造力

## Shell 脚本基础

### 基本语法

```bash
#!/bin/bash
# 脚本头部，指定解释器

# 变量
NAME="World"
echo "Hello, $NAME!"

# 条件判断
if [ "$NAME" == "World" ]; then
    echo "Hello, World!"
else
    echo "Hello, $NAME!"
fi

# 循环
for i in {1..5}; do
    echo "Number: $i"
done

# 函数
greet() {
    echo "Hello, $1!"
}

greet "Alice"
```

### 常用命令

| 命令 | 功能 |
|------|------|
| `echo` | 输出文本 |
| `cd` | 切换目录 |
| `ls` | 列出文件 |
| `cp` | 复制文件 |
| `mv` | 移动文件 |
| `rm` | 删除文件 |
| `grep` | 搜索文本 |
| `find` | 查找文件 |
| `sed` | 文本替换 |
| `awk` | 文本处理 |

## AI 辅助脚本生成

### 示例 1：自动化测试

```bash
# 用户需求
> create a script to run all tests and generate a report

# AI 生成脚本
#!/bin/bash
# 自动化测试脚本

echo "Running tests..."
npm test > test_results.txt

if [ $? -eq 0 ]; then
    echo "✓ All tests passed"
    echo "Test Report:" > report.txt
    cat test_results.txt >> report.txt
else
    echo "✗ Tests failed"
    exit 1
fi
```

### 示例 2：自动化部署

```bash
# 用户需求
> create a deployment script for my Node.js app

# AI 生成脚本
#!/bin/bash
# 部署脚本

# 拉取最新代码
echo "Pulling latest code..."
git pull origin main

# 安装依赖
echo "Installing dependencies..."
npm install

# 运行测试
echo "Running tests..."
npm test

if [ $? -ne 0 ]; then
    echo "Tests failed, aborting deployment"
    exit 1
fi

# 构建
echo "Building..."
npm run build

# 部署
echo "Deploying..."
npm run deploy

echo "Deployment complete!"
```

### 示例 3：数据库备份

```bash
# 用户需求
> create a script to backup PostgreSQL database

# AI 生成脚本
#!/bin/bash
# 数据库备份脚本

DB_NAME="mydb"
BACKUP_DIR="/backups"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/${DB_NAME}_${DATE}.sql"

# 创建备份目录
mkdir -p $BACKUP_DIR

# 备份数据库
echo "Backing up database..."
pg_dump $DB_NAME > $BACKUP_FILE

# 压缩备份
gzip $BACKUP_FILE

# 删除 7 天前的备份
find $BACKUP_DIR -name "*.sql.gz" -mtime +7 -delete

echo "Backup complete: ${BACKUP_FILE}.gz"
```

## 工作流自动化

### Makefile

```makefile
.PHONY: install test build deploy clean

install:
    npm install

test:
    npm test

build:
    npm run build

deploy: test build
    npm run deploy

clean:
    rm -rf node_modules dist
```

### package.json scripts

```json
{
  "scripts": {
    "dev": "nodemon src/index.js",
    "test": "jest",
    "build": "webpack --mode production",
    "deploy": "npm run build && rsync -av dist/ user@server:/var/www/app/"
  }
}
```

## 高级自动化技术

### 1. 条件执行

```bash
#!/bin/bash
# 只在文件变化时执行

if [ "src/main.py" -nt "output.txt" ]; then
    python src/main.py > output.txt
    echo "Generated output.txt"
else
    echo "output.txt is up to date"
fi
```

### 2. 并行执行

```bash
#!/bin/bash
# 并行运行多个任务

# 使用 & 在后台运行
task1 &
task2 &
task3 &

# 等待所有任务完成
wait

echo "All tasks completed"
```

### 3. 错误处理

```bash
#!/bin/bash
# 错误处理

set -e  # 任何命令失败时退出
set -u  # 使用未定义变量时报错
set -o pipefail  # 管道中任何命令失败时退出

# 捕获错误
trap 'echo "Error occurred at line $LINENO"' ERR
```

## 下一步

现在你已经了解了终端自动化，让我们完成本周的作业：

→ [作业：使用 Warp 进行代理开发](03-assignment.md)
