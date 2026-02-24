# GitHub 推送指南

游戏代码已经准备好并提交到本地 Git 仓库！现在需要推送到 GitHub。

## 方法一：手动创建 GitHub 仓库并推送

### 1. 在 GitHub 上创建新仓库

1. 访问 https://github.com/new
2. 仓库名称: `snake-game`
3. 描述: `🐍 经典贪吃蛇游戏 - HTML5 Canvas + JavaScript`
4. 选择 Public 或 Private
5. **不要**勾选 "Add a README file"、"Add .gitignore"、"Choose a license"
6. 点击 "Create repository"

### 2. 推送到 GitHub

创建仓库后，GitHub 会显示推送命令。或者使用以下命令：

```bash
# 进入项目目录
cd ~/workspace/snake-game

# 添加远程仓库（替换 YOUR_USERNAME 为你的 GitHub 用户名）
git remote add origin https://github.com/YOUR_USERNAME/snake-game.git

# 推送到 GitHub
git branch -M main
git push -u origin main
```

### 3. 认证方式

推送时需要认证，有两种方式：

#### 方式 A: 使用 Personal Access Token (推荐)
1. 访问 https://github.com/settings/tokens
2. 点击 "Generate new token (classic)"
3. 勾选 `repo` 权限
4. 生成 token 并复制
5. 推送时在密码处输入 token

#### 方式 B: 使用 SSH 密钥
```bash
# 生成 SSH 密钥
ssh-keygen -t ed25519 -C "your_email@example.com"

# 查看公钥
cat ~/.ssh/id_ed25519.pub

# 将公钥添加到 GitHub: https://github.com/settings/ssh/new

# 修改远程地址为 SSH
git remote set-url origin git@github.com:YOUR_USERNAME/snake-game.git
```

## 方法二: 使用 GitHub CLI (如果已安装)

```bash
# 安装 GitHub CLI
# Windows: winget install GitHub.cli
# macOS: brew install gh
# Linux: sudo apt install gh

# 登录
gh auth login

# 创建并推送仓库
gh repo create snake-game --public --source=. --push
```

## 当前仓库状态

✅ 本地仓库已初始化
✅ 代码已提交 (commit: 7a76d1b)
✅ 包含文件:
   - index.html (游戏主文件)
   - README.md (项目说明)

⏳ 等待推送到 GitHub

## 需要的信息

请提供以下信息之一，我可以帮你完成推送：

1. GitHub 用户名
2. 或者 GitHub Personal Access Token
3. 或者告诉我你已经创建了仓库的 URL

---

**提示**: 如果你在 GitHub 上已经创建了仓库，请告诉我仓库 URL，我会立即帮你推送代码！
