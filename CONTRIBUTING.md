# 贡献指南 / Contributing Guide

本文档详细说明如何为本项目做出贡献，特别是解决 **Git 推送权限问题**。

## 📋 目录

- [理解 GitHub 权限模型](#理解-github-权限模型)
- [两种贡献方式](#两种贡献方式)
  - [方式 1: Fork 工作流（推荐）](#方式-1-fork-工作流推荐)
  - [方式 2: 协作者直接推送](#方式-2-协作者直接推送)
- [常见错误解析](#常见错误解析)
- [完整工作流程示例](#完整工作流程示例)
- [最佳实践](#最佳实践)

---

## 理解 GitHub 权限模型

### Public 仓库 ≠ 任何人都可以推送

这是很多初学者的常见误解。GitHub 的权限模型如下：

| 仓库可见性 | 谁可以查看 | 谁可以克隆 | 谁可以推送 |
|-----------|-----------|-----------|-----------|
| **Public（公开）** | ✅ 所有人 | ✅ 所有人 | ❌ 仅协作者 + 所有者 |
| **Private（私有）** | ❌ 仅协作者 + 所有者 | ❌ 仅协作者 + 所有者 | ❌ 仅协作者 + 所有者 |

**关键点：**
- 📖 **Read（读取）**: Public 仓库对所有人开放
- ✏️ **Write（写入）**: 需要明确的权限授予
- 🔒 **Admin（管理）**: 仅所有者和被授予管理员权限的协作者

### 为什么需要这样的权限控制？

1. **安全性**: 防止恶意代码被推送到仓库
2. **质量控制**: 确保所有代码变更经过审查
3. **追溯性**: 明确谁做了什么更改
4. **协作规范**: 强制使用 Pull Request 流程

---

## 两种贡献方式

根据你与仓库的关系，选择合适的贡献方式：

### 方式 1: Fork 工作流（推荐）

**适用场景：**
- ✅ 外部贡献者
- ✅ 考核候选人（没有协作者权限）
- ✅ 临时贡献
- ✅ 学习 GitHub 标准协作流程

**优势：**
- 不需要任何特殊权限
- 完全独立的工作环境
- 符合开源项目标准流程
- 便于学习和练习

#### 步骤 1: Fork 仓库

1. 访问原仓库: https://github.com/westlakeomics/Python_Git_exam
2. 点击右上角的 **"Fork"** 按钮
3. 选择你的账户作为 Fork 目标
4. 等待 Fork 完成

#### 步骤 2: 克隆你的 Fork

```bash
# 克隆你自己 Fork 的仓库（注意 URL 包含你的用户名）
git clone https://github.com/你的用户名/Python_Git_exam.git

# 进入目录
cd Python_Git_exam

# 验证远程仓库
git remote -v
# 应该显示：
# origin  https://github.com/你的用户名/Python_Git_exam.git (fetch)
# origin  https://github.com/你的用户名/Python_Git_exam.git (push)
```

#### 步骤 3: 添加上游仓库（可选但推荐）

```bash
# 添加原仓库为 upstream
git remote add upstream https://github.com/westlakeomics/Python_Git_exam.git

# 验证
git remote -v
# 现在应该显示：
# origin    https://github.com/你的用户名/Python_Git_exam.git (fetch)
# origin    https://github.com/你的用户名/Python_Git_exam.git (push)
# upstream  https://github.com/westlakeomics/Python_Git_exam.git (fetch)
# upstream  https://github.com/westlakeomics/Python_Git_exam.git (push)
```

#### 步骤 4: 创建特性分支

```bash
# 确保在 main 分支
git checkout main

# 创建新分支（将 username 替换为你的用户名）
git checkout -b fix/username

# 或者使用 task 前缀
git checkout -b task/username-fix
```

#### 步骤 5: 进行修改

```bash
# 创建虚拟环境
conda create --name myenv python=3.11 -y
conda activate myenv

# 安装依赖
pip install -r requirements.txt

# 修改 src/bad_style.py
# 使用你喜欢的编辑器...

# 本地测试
pytest tests/
ruff check .
black --check .
isort --check-only .
```

#### 步骤 6: 提交更改

```bash
# 查看修改
git status
git diff src/bad_style.py

# 添加文件
git add src/bad_style.py

# 提交（使用清晰的提交信息）
git commit -m "fix: apply PEP 8 standards to bad_style.py"

# 如果有多个逻辑变更，可以分多次提交
git add src/bad_style.py
git commit -m "fix: improve naming conventions"

git add src/bad_style.py
git commit -m "fix: add docstrings and type hints"
```

#### 步骤 7: 推送到你的 Fork

```bash
# 推送到你自己的 Fork（origin）
git push -u origin fix/username

# 第一次推送后，之后可以简化为：
git push
```

**此时不会报 403 错误**，因为你在推送到**你自己的**仓库。

#### 步骤 8: 创建 Pull Request

1. **方式 A: GitHub 自动提示**
   - 推送后，访问原仓库: https://github.com/westlakeomics/Python_Git_exam
   - 页面顶部会显示黄色提示条: "Your recently pushed branches"
   - 点击 **"Compare & pull request"**

2. **方式 B: 手动创建**
   - 访问原仓库并点击 **"Pull requests"** 标签
   - 点击 **"New pull request"**
   - 点击 **"compare across forks"**
   - 选择：
     - **base repository**: `westlakeomics/Python_Git_exam` **base**: `main`
     - **head repository**: `你的用户名/Python_Git_exam` **compare**: `fix/username`
   - 点击 **"Create pull request"**

3. **填写 PR 信息**
   ```
   标题: Fix: Apply PEP 8 standards to bad_style.py
   
   描述:
   ## 修复内容
   
   本 PR 修复了 `src/bad_style.py` 中的以下问题：
   
   - ✅ 导入排序（imports）
   - ✅ 代码格式化（formatting）
   - ✅ 命名规范（naming conventions）
   - ✅ 文档字符串（docstrings）
   - ✅ 类型注解（type hints）
   
   ## 测试结果
   
   - ✅ pytest 全部通过
   - ✅ ruff check 无警告
   - ✅ black --check 通过
   - ✅ isort --check-only 通过
   
   ## 检查清单
   
   - [x] 代码符合 PEP 8 规范
   - [x] 所有测试通过
   - [x] 未改变程序功能
   - [x] 提交信息清晰
   ```

4. **等待 CI 验证**
   - PR 创建后，GitHub Actions 会自动运行
   - 检查 PR 页面的 "Checks" 标签
   - 等待所有检查变为绿色 ✅

5. **等待审查和合并**
   - 仓库维护者会审查你的代码
   - 如果需要修改，在本地修改后再次推送：
     ```bash
     # 修改代码...
     git add .
     git commit -m "fix: address review comments"
     git push
     ```
   - PR 会自动更新

#### 步骤 9: 同步上游更新（可选）

如果原仓库有新的提交，你需要同步：

```bash
# 获取上游更新
git fetch upstream

# 切换到 main 分支
git checkout main

# 合并上游的 main
git merge upstream/main

# 推送到你的 Fork
git push origin main

# 如果需要更新你的特性分支
git checkout fix/username
git merge main
# 或者使用 rebase
git rebase main
```

---

### 方式 2: 协作者直接推送

**适用场景：**
- ✅ 团队成员
- ✅ 已被添加为 Collaborator 的考核候选人
- ✅ 长期贡献者

**前置条件：**
- 必须被仓库所有者添加为协作者

#### 步骤 1: 请求协作者权限

联系仓库管理员（通过 Email、Issue 或其他沟通方式），请求协作者权限。

管理员需要：
1. 访问仓库 **Settings** → **Collaborators and teams**
2. 点击 **"Add people"**
3. 输入你的 GitHub 用户名
4. 选择权限级别（通常是 **Write**）
5. 发送邀请

#### 步骤 2: 接受邀请

你会收到：
- 📧 GitHub 发送的邮件邀请
- 🔔 GitHub 通知

接受邀请的方式：
- 点击邮件中的链接
- 访问: `https://github.com/westlakeomics/Python_Git_exam/invitations`
- 或在 GitHub 通知中心点击接受

#### 步骤 3: 克隆原仓库

```bash
# 现在可以直接克隆原仓库
git clone https://github.com/westlakeomics/Python_Git_exam.git
cd Python_Git_exam

# 验证远程仓库
git remote -v
# 应该显示：
# origin  https://github.com/westlakeomics/Python_Git_exam.git (fetch)
# origin  https://github.com/westlakeomics/Python_Git_exam.git (push)
```

#### 步骤 4-7: 与 Fork 工作流相同

创建分支、修改代码、提交、推送的步骤与 Fork 工作流完全相同。

唯一区别：
```bash
# 推送时，直接推送到原仓库
git push -u origin fix/username
```

#### 步骤 8: PR 会自动创建

- 由于配置了 `.github/workflows/auto-pr.yml`
- 当你推送新分支后，PR 会**自动创建**
- 无需手动创建 PR

---

## 常见错误解析

### 错误 1: Permission denied (403)

```
remote: Permission to westlakeomics/Python_Git_exam.git denied to username.
fatal: unable to access 'https://github.com/westlakeomics/Python_Git_exam.git/': The requested URL returned error: 403
```

**原因：**
- 你不是仓库的协作者
- 你正在尝试直接推送到原仓库

**解决方案：**
- ✅ 使用 Fork 工作流（见上文）
- ✅ 或请求协作者权限

---

### 错误 2: Repository not found (404)

```
remote: Repository not found.
fatal: repository 'https://github.com/westlakeomics/Python_Git_exam.git/' not found
```

**可能原因：**
- URL 拼写错误
- 仓库不存在或已被删除
- 你没有访问权限（如果是私有仓库）

**解决方案：**
```bash
# 检查远程 URL
git remote -v

# 如果 URL 错误，更正它
git remote set-url origin https://github.com/正确的用户名/Python_Git_exam.git
```

---

### 错误 3: Authentication failed

```
remote: Invalid username or password.
fatal: Authentication failed for 'https://github.com/...'
```

**原因：**
- GitHub 已不再支持密码认证（自 2021 年 8 月起）
- 需要使用 Personal Access Token (PAT)

**解决方案 A: 使用 Personal Access Token**

1. 生成 PAT:
   - 访问: https://github.com/settings/tokens
   - 点击 **"Generate new token"** → **"Generate new token (classic)"**
   - 选择权限: `repo`（完整仓库控制）
   - 设置过期时间
   - 点击 **"Generate token"**
   - **立即复制 token**（之后无法再次查看）

2. 使用 PAT 推送:
   ```bash
   # HTTPS URL 格式
   git push
   # 当提示输入密码时，粘贴 PAT（不是你的 GitHub 密码）
   
   # 或者在 URL 中包含 token（不推荐，因为会暴露在命令历史中）
   git remote set-url origin https://TOKEN@github.com/用户名/仓库名.git
   ```

**解决方案 B: 使用 SSH（推荐）**

1. 生成 SSH 密钥:
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   # 或使用 RSA
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

2. 添加 SSH 密钥到 GitHub:
   - 复制公钥: `cat ~/.ssh/id_ed25519.pub`
   - 访问: https://github.com/settings/keys
   - 点击 **"New SSH key"**
   - 粘贴公钥并保存

3. 修改远程 URL 为 SSH:
   ```bash
   git remote set-url origin git@github.com:用户名/Python_Git_exam.git
   
   # 验证
   git remote -v
   
   # 测试连接
   ssh -T git@github.com
   ```

---

### 错误 4: Updates were rejected

```
! [rejected]        fix/username -> fix/username (non-fast-forward)
error: failed to push some refs to 'https://github.com/...'
hint: Updates were rejected because the tip of your current branch is behind
```

**原因：**
- 远程分支有新的提交
- 你的本地分支落后于远程

**解决方案：**

```bash
# 方案 A: 拉取并合并
git pull origin fix/username
# 解决可能的冲突
git push origin fix/username

# 方案 B: 拉取并变基（保持历史整洁）
git pull --rebase origin fix/username
# 解决可能的冲突
git push origin fix/username

# 方案 C: 如果确认要覆盖远程（谨慎使用）
git push --force-with-lease origin fix/username
```

**⚠️ 警告：** 永远不要在 main 分支使用 `--force` 或 `--force-with-lease`

---

## 完整工作流程示例

### 场景：外部考核候选人使用 Fork 工作流

```bash
# 1. Fork 仓库（在 GitHub 网页操作）

# 2. 克隆你的 Fork
git clone https://github.com/zhangsan/Python_Git_exam.git
cd Python_Git_exam

# 3. 添加上游
git remote add upstream https://github.com/westlakeomics/Python_Git_exam.git

# 4. 创建环境
conda create -n exam python=3.11 -y
conda activate exam
pip install -r requirements.txt

# 5. 创建分支
git checkout -b fix/zhangsan

# 6. 修改代码
# 编辑 src/bad_style.py...

# 7. 本地验证
pytest tests/
ruff check .
black --check .
isort --check-only .

# 8. 提交
git add src/bad_style.py
git commit -m "fix: apply PEP 8 standards"

# 9. 推送到你的 Fork
git push -u origin fix/zhangsan

# 10. 在 GitHub 网页创建 PR
# 访问: https://github.com/westlakeomics/Python_Git_exam
# 点击 "Compare & pull request"

# 11. 等待 CI 验证和审查

# 12. 如果需要修改
# 编辑代码...
git add .
git commit -m "fix: address review feedback"
git push  # PR 自动更新

# 13. PR 合并后，清理
git checkout main
git branch -d fix/zhangsan  # 删除本地分支

# 14. 同步更新（可选）
git fetch upstream
git merge upstream/main
git push origin main
```

---

## 最佳实践

### ✅ DO（推荐做法）

1. **使用描述性的分支名**
   - ✅ `fix/zhangsan-pep8`
   - ✅ `task/lisi-refactor`
   - ❌ `test`, `temp`, `new-branch`

2. **提交信息清晰**
   - ✅ `fix: improve variable naming in bad_style.py`
   - ✅ `refactor: extract validation logic into separate function`
   - ❌ `update`, `fix bug`, `changes`

3. **小步提交**
   - 每个提交专注于一个逻辑变更
   - 便于代码审查
   - 易于回滚

4. **推送前本地测试**
   ```bash
   pytest tests/
   ruff check .
   black --check .
   isort --check-only .
   ```

5. **保持分支更新**
   ```bash
   # 定期同步 main
   git checkout main
   git pull upstream main
   git checkout fix/username
   git merge main
   ```

### ❌ DON'T（避免做法）

1. **不要直接修改 main 分支**
   - 始终在特性分支工作
   - main 应该保持干净

2. **不要提交未测试的代码**
   - 确保本地测试通过
   - 避免 CI 失败

3. **不要强制推送到共享分支**
   - `git push --force` 很危险
   - 如果必须，使用 `--force-with-lease`

4. **不要在提交中包含敏感信息**
   - 密码、token、密钥
   - 使用 `.gitignore` 排除

5. **不要忽略 Code Review 反馈**
   - 认真对待审查意见
   - 及时响应和修改

---

## 额外资源

### 官方文档

- [GitHub Docs: Fork a repo](https://docs.github.com/en/get-started/quickstart/fork-a-repo)
- [GitHub Docs: Creating a pull request from a fork](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork)
- [GitHub Docs: About permissions](https://docs.github.com/en/get-started/learning-about-github/access-permissions-on-github)
- [GitHub Docs: SSH authentication](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

### 项目文档

- [README.md](README.md) - 项目主文档
- [.github/WORKFLOW.md](.github/WORKFLOW.md) - 自动化工作流说明
- [.github/BRANCH_PROTECTION.md](.github/BRANCH_PROTECTION.md) - 分支保护配置

### 相关工具

- [Git 官方文档](https://git-scm.com/doc)
- [Pro Git 中文版](https://git-scm.com/book/zh/v2)
- [GitHub CLI (gh)](https://cli.github.com/)

---

## 获取帮助

如果遇到问题：

1. **查看本文档** - 大多数常见问题已覆盖
2. **搜索 Issues** - 可能有人遇到过相同问题
3. **创建 Issue** - 详细描述你的问题
   - 包含错误消息
   - 包含你尝试的步骤
   - 包含你的环境信息（OS、Git 版本等）

---

## 许可证

本项目用于教学和考核目的。贡献者应遵守项目的使用规范。

---

**最后更新**: 2025-12-25  
**维护者**: westlakeomics 团队
