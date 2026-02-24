# 部署指南

本文档介绍如何将此 Docsify 项目部署到 GitHub Pages。

## 方法一：使用 GitHub Actions 自动部署（推荐）

此项目已配置好 GitHub Actions 工作流，可以自动部署到 GitHub Pages。

### 步骤 1：创建 GitHub 仓库

1. 在 GitHub 上创建新仓库，命名为 `modern-software-dev-zh`（或你喜欢的名称）
2. 初始化本地 Git 仓库（如果还没有）：

```bash
git init
git add .
git commit -m "Initial commit: 现代软件开发者中文教程"
```

3. 关联远程仓库：

```bash
git remote add origin https://github.com/你的用户名/modern-software-dev-zh.git
```

4. 推送到 GitHub：

```bash
git branch -M main
git push -u origin main
```

### 步骤 2：启用 GitHub Pages

1. 进入 GitHub 仓库的 **Settings** → **Pages**
2. 在 **Build and deployment** 下：
   - **Source**: 选择 `GitHub Actions`
3. 点击 **Save**

### 步骤 3：等待部署

GitHub Actions 会自动运行，部署完成后你会看到：
```
✓ Deployment successful
```

访问你的网站：`https://你的用户名.github.io/modern-software-dev-zh/`

### 工作流说明

项目已配置 `.github/workflows/deploy.yml` 工作流，它会：

1. 检出代码
2. 直接部署整个项目到 GitHub Pages

Docsify 是一个静态站点生成器，不需要构建步骤，可以直接部署 Markdown 文件。

---

## 方法二：使用 gh-pages 部署

### 步骤 1：安装依赖

```bash
npm install -g gh-pages
```

### 步骤 2：本地预览

```bash
docsify serve .
```

访问 `http://localhost:3000` 查看效果。

### 步骤 3：部署到 GitHub Pages

```bash
gh-pages -d .
```

---

## 方法三：手动部署

### 步骤 1：创建 gh-pages 分支

```bash
git checkout --orphan gh-pages
git rm -rf .
git checkout main -- .
echo "" > .nojekyll
git add .
git commit -m "Deploy to GitHub Pages"
```

### 步骤 2：推送分支

```bash
git push origin gh-pages
```

### 步骤 3：设置 GitHub Pages

1. 进入仓库 **Settings** → **Pages**
2. **Source**: 选择 `Deploy from a branch`
3. **Branch**: 选择 `gh-pages`，文件夹选择 `/ (root)`
4. 点击 **Save**

---

## 本地开发

### 安装 Docsify

```bash
npm install -g docsify-cli
```

### 启动本地服务器

```bash
docsify serve .
```

访问 `http://localhost:3000`

### 热重载

Docsify 会自动检测文件变化并重新加载页面。

---

## 更新内容

### 修改内容

直接编辑 Markdown 文件，Docsify 会自动检测变化。

### 添加新页面

1. 创建新的 Markdown 文件
2. 在 `_sidebar.md` 中添加新页面的链接

```markdown
- [新页面](新页面.md)
```

### 提交更改

```bash
git add .
git commit -m "更新内容"
git push
```

GitHub Actions 会自动重新部署。

---

## 常见问题

### Q: 部署后页面 404？

A: 检查以下几点：
1. GitHub Pages 是否已启用
2. 分支是否正确推送
3. Actions 是否成功运行
4. 等待几分钟，GitHub Pages 需要时间构建
5. 确保根目录有 `index.html` 文件

### Q: 3级目录（如 week1/01-llm-basics.md）点击后404？

A: 本项目已配置以下解决方案：

**Docsify 配置**（index.html）：
- `loadSidebar: true` - 自动加载当前目录的 `_sidebar.md` 文件
- `routerMode: 'hash'` - 使用哈希路由，避免服务器路由问题
- `subMaxLevel: 3` - 侧边栏显示3级目录

**工作原理**：
1. 访问根目录时，加载根目录的 `_sidebar.md`（包含所有week的链接）
2. 点击 `week1/02-prompt-engineering.md` 时，Docsify自动加载 `week1/_sidebar.md`
3. 子目录的侧边栏使用相对路径（如 `02-prompt-engineering.md`），确保正确导航

**Jekyll 禁用**（.nojekyll）：
- 根目录和 docs 目录都有 `.nojekyll` 文件
- 防止 Jekyll 忽略以下划线开头的文件（如 `_sidebar.md`）

**本地测试**：
```bash
# 安装 docsify-cli
npm install -g docsify-cli

# 启动本地服务器
docsify serve .

# 访问测试
# http://localhost:3000/#/week1/02-prompt-engineering
```

### Q: 中文显示乱码？

A: 确保 `index.html` 中设置了正确的字符集：

```html
<meta charset="UTF-8">
```

### Q: 如何自定义域名？

A: 在 GitHub 仓库设置中：
1. **Settings** → **Pages**
2. **Custom domain**: 输入你的域名
3. 按照提示配置 DNS

### Q: 如何修改主题？

A: 在 `index.html` 中修改主题链接：

```html
<!-- Vue 主题 -->
<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css">

<!-- 其他主题 -->
<!-- <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/dark.css"> -->
<!-- <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/buble.css"> -->
```

### Q: 如何添加更多插件？

A: 在 `index.html` 中添加插件脚本：

```html
<script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/插件名.min.js"></script>
```

---

## 相关资源

- [Docsify 官方文档](https://docsify.js.org/)
- [GitHub Pages 文档](https://docs.github.com/en/pages)
- [GitHub Actions 文档](https://docs.github.com/en/actions)
