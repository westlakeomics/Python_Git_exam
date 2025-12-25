# Git 权限问题常见问答 / Git Permission FAQ

## 🔒 关于 Git Push 403 权限错误的完整解答

### 问题现象

当你尝试推送代码时，遇到类似以下的错误：

```bash
$ git push -u origin fix/test
remote: Permission to westlakeomics/Python_Git_exam.git denied to zhiyuajun.
fatal: unable to access 'https://github.com/westlakeomics/Python_Git_exam.git/': The requested URL returned error: 403
```

---

## 📚 核心概念理解

### Q1: 为什么 Public 仓库我不能推送？

**A:** 这是 GitHub 安全设计的核心原则：

**Public（公开）≠ Write Access（写权限）**

| 操作 | Public 仓库 | Private 仓库 |
|-----|------------|-------------|
| **查看代码** | ✅ 所有人 | ❌ 仅协作者 |
| **克隆仓库** | ✅ 所有人 | ❌ 仅协作者 |
| **Fork 仓库** | ✅ 所有人 | ❌ 仅协作者 |
| **创建 Issue** | ✅ 所有人 | ❌ 仅协作者 |
| **推送代码** | ❌ 仅协作者 | ❌ 仅协作者 |
| **合并 PR** | ❌ 仅维护者 | ❌ 仅维护者 |

**类比理解：**
- Public 仓库就像**公共图书馆**：
  - ✅ 任何人可以进去**阅读**书籍（查看代码）
  - ✅ 任何人可以**复印**书籍（克隆/Fork）
  - ❌ 但不能直接**修改或添加**图书馆的书（推送代码）
  - ✅ 可以提交**建议**（Pull Request）
  
### Q2: 那 Public 仓库的意义是什么？

**A:** Public 仓库的价值在于：

1. **透明性**：任何人都能查看和学习代码
2. **协作性**：通过 Fork + PR 流程接受外部贡献
3. **可访问性**：无需登录即可克隆和使用
4. **社区参与**：可以创建 Issue、参与讨论

---

## 🛠️ 解决方案

### Q3: 我应该选择哪种方式贡献代码？

**A:** 根据你的身份选择：

#### 方案对比表

| 特征 | Fork 工作流 | 协作者直接推送 |
|-----|-----------|-------------|
| **适用人群** | 外部贡献者、考核候选人 | 团队成员、长期贡献者 |
| **需要权限** | ❌ 不需要 | ✅ 需要被添加为协作者 |
| **工作副本** | 你自己账户下的 Fork | 原仓库的分支 |
| **推送目标** | 你的 Fork | 原仓库 |
| **PR 创建** | 手动创建跨仓库 PR | 自动创建（本项目配置） |
| **学习价值** | ⭐⭐⭐⭐⭐ 符合开源标准流程 | ⭐⭐⭐ 团队协作流程 |
| **推荐度** | 🌟 推荐给初学者和外部人员 | 团队内部使用 |

**决策树：**

```
你是否是仓库的协作者？
│
├─ 是 → 使用方案 2（直接推送）
│
└─ 否 → 你能联系管理员请求协作者权限吗？
       │
       ├─ 能 → 请求权限后使用方案 2
       │
       └─ 不能 → 使用方案 1（Fork 工作流）✅ 推荐
```

### Q4: Fork 工作流的详细步骤是什么？

**A:** 完整步骤（假设你的用户名是 `zhangsan`）：

#### 步骤 1: Fork 仓库（GitHub 网页操作）

1. 访问: https://github.com/westlakeomics/Python_Git_exam
2. 点击右上角的 **"Fork"** 按钮
3. 选择你的账户（`zhangsan`）
4. 等待 Fork 完成（通常几秒钟）
5. 你现在有了一个副本: `https://github.com/zhangsan/Python_Git_exam`

#### 步骤 2: 克隆你的 Fork（本地操作）

```bash
# ⚠️ 注意：这里的 URL 是你自己的 Fork，不是原仓库
git clone https://github.com/zhangsan/Python_Git_exam.git

# 进入目录
cd Python_Git_exam

# 验证远程地址（确保是你的 Fork）
git remote -v
# 输出：
# origin  https://github.com/zhangsan/Python_Git_exam.git (fetch)
# origin  https://github.com/zhangsan/Python_Git_exam.git (push)
```

#### 步骤 3: 配置上游仓库

```bash
# 添加原仓库作为 upstream
git remote add upstream https://github.com/westlakeomics/Python_Git_exam.git

# 再次验证
git remote -v
# 输出：
# origin    https://github.com/zhangsan/Python_Git_exam.git (fetch)
# origin    https://github.com/zhangsan/Python_Git_exam.git (push)
# upstream  https://github.com/westlakeomics/Python_Git_exam.git (fetch)
# upstream  https://github.com/westlakeomics/Python_Git_exam.git (push)
```

**理解 origin 和 upstream：**
- `origin`: 你的 Fork（你可以推送）
- `upstream`: 原仓库（你只能拉取）

#### 步骤 4: 创建分支并工作

```bash
# 从 main 创建新分支
git checkout -b fix/zhangsan

# 修改代码...
# 编辑 src/bad_style.py

# 提交更改
git add src/bad_style.py
git commit -m "fix: apply PEP 8 standards"

# 推送到你的 Fork（不会报 403 错误！）
git push -u origin fix/zhangsan
```

**为什么不报 403？**  
因为你在推送到 `origin`（你自己的 Fork），你对自己的仓库有完全控制权。

#### 步骤 5: 创建 Pull Request

**自动提示方式：**
1. 推送后，访问原仓库: https://github.com/westlakeomics/Python_Git_exam
2. 页面顶部会显示黄色横幅: "Your recently pushed branches: fix/zhangsan"
3. 点击 **"Compare & pull request"** 按钮

**手动创建方式：**
1. 访问原仓库: https://github.com/westlakeomics/Python_Git_exam
2. 点击 **"Pull requests"** 标签
3. 点击 **"New pull request"**
4. 点击 **"compare across forks"** 链接
5. 设置：
   - **base repository**: `westlakeomics/Python_Git_exam`
   - **base**: `main`
   - **head repository**: `zhangsan/Python_Git_exam`
   - **compare**: `fix/zhangsan`
6. 点击 **"Create pull request"**

#### 步骤 6: 等待验证和审查

- PR 创建后，CI 会自动运行（在**原仓库**中）
- 等待所有检查通过 ✅
- 响应审查反馈（如果有）

#### 步骤 7: 处理审查反馈（如需要）

```bash
# 在同一分支继续修改
git checkout fix/zhangsan

# 修改代码...
# 编辑 src/bad_style.py

# 提交新的更改
git add src/bad_style.py
git commit -m "fix: address review comments"

# 推送（PR 会自动更新）
git push
```

### Q5: 如果我已经克隆了原仓库怎么办？

**A:** 如果你已经克隆了原仓库（`westlakeomics/Python_Git_exam`），需要调整远程地址：

```bash
# 1. 首先在 GitHub 网页 Fork 仓库

# 2. 查看当前远程配置
git remote -v
# 输出可能是：
# origin  https://github.com/westlakeomics/Python_Git_exam.git (fetch)
# origin  https://github.com/westlakeomics/Python_Git_exam.git (push)

# 3. 将 origin 改为你的 Fork
git remote set-url origin https://github.com/zhangsan/Python_Git_exam.git

# 4. 将原仓库添加为 upstream
git remote add upstream https://github.com/westlakeomics/Python_Git_exam.git

# 5. 验证
git remote -v
# 现在应该显示：
# origin    https://github.com/zhangsan/Python_Git_exam.git (fetch)
# origin    https://github.com/zhangsan/Python_Git_exam.git (push)
# upstream  https://github.com/westlakeomics/Python_Git_exam.git (fetch)
# upstream  https://github.com/westlakeomics/Python_Git_exam.git (push)

# 6. 如果你已经有本地分支，推送到你的 Fork
git checkout fix/zhangsan
git push -u origin fix/zhangsan
```

---

## 🔐 认证相关问题

### Q6: 推送时要求输入密码，应该输入什么？

**A:** GitHub 从 2021 年 8 月起不再支持密码认证。你需要使用：

#### 选项 1: Personal Access Token (PAT) - 适合 HTTPS

**生成 PAT：**

1. 访问: https://github.com/settings/tokens
2. 点击 **"Generate new token"** → **"Generate new token (classic)"**
3. 设置说明（如 "Python_Git_exam"）
4. 选择有效期（建议 30 或 90 天）
5. 勾选权限：
   - ✅ `repo`（完整仓库控制）
6. 点击 **"Generate token"**
7. **立即复制 token**（格式类似 `ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`）

**使用 PAT：**

```bash
# 当推送时提示输入密码
git push -u origin fix/zhangsan
Username: zhangsan
Password: [粘贴你的 PAT，不是 GitHub 密码]
```

**保存 PAT（避免每次输入）：**

```bash
# 使用 Git 凭据存储
git config --global credential.helper store

# 或使用缓存（临时存储）
git config --global credential.helper cache

# macOS 使用钥匙串
git config --global credential.helper osxkeychain

# Windows 使用凭据管理器
git config --global credential.helper wincred
```

#### 选项 2: SSH 密钥 - 推荐长期使用

**生成 SSH 密钥：**

```bash
# 生成新的 SSH 密钥
ssh-keygen -t ed25519 -C "your_email@example.com"

# 如果系统不支持 ed25519，使用 RSA
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# 按提示操作：
# - 保存位置：直接回车（使用默认 ~/.ssh/id_ed25519）
# - 密码：可选（建议设置）

# 启动 SSH agent
eval "$(ssh-agent -s)"

# 添加密钥到 agent
ssh-add ~/.ssh/id_ed25519
```

**添加到 GitHub：**

1. 复制公钥：
   ```bash
   cat ~/.ssh/id_ed25519.pub
   # 或使用剪贴板（macOS）
   pbcopy < ~/.ssh/id_ed25519.pub
   # Linux
   xclip -selection clipboard < ~/.ssh/id_ed25519.pub
   ```

2. 访问: https://github.com/settings/keys
3. 点击 **"New SSH key"**
4. 标题：如 "My Laptop"
5. 密钥类型：Authentication Key
6. 粘贴公钥内容
7. 点击 **"Add SSH key"**

**测试连接：**

```bash
ssh -T git@github.com
# 输出：
# Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

**修改远程 URL 为 SSH：**

```bash
# 查看当前 URL
git remote -v

# 改为 SSH
git remote set-url origin git@github.com:zhangsan/Python_Git_exam.git

# 验证
git remote -v
# 应该显示：
# origin  git@github.com:zhangsan/Python_Git_exam.git (fetch)
# origin  git@github.com:zhangsan/Python_Git_exam.git (push)
```

现在推送不再需要输入密码！

---

## 🔄 同步和更新

### Q7: 如何保持我的 Fork 与原仓库同步？

**A:** 定期同步可以获取最新更改：

```bash
# 1. 确保已添加 upstream
git remote -v | grep upstream
# 如果没有，添加它：
git remote add upstream https://github.com/westlakeomics/Python_Git_exam.git

# 2. 获取上游更新
git fetch upstream

# 3. 切换到 main 分支
git checkout main

# 4. 合并上游的 main
git merge upstream/main

# 5. 推送到你的 Fork
git push origin main

# 6. （可选）更新你的特性分支
git checkout fix/zhangsan
git merge main
# 或使用 rebase 保持历史整洁
git rebase main
```

**自动化脚本（可选）：**

创建文件 `sync-fork.sh`：

```bash
#!/bin/bash
echo "Syncing fork with upstream..."
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
echo "Sync complete!"
```

使用：
```bash
chmod +x sync-fork.sh
./sync-fork.sh
```

---

## 🚨 常见陷阱

### Q8: 为什么我的 PR 没有触发 CI？

**A:** 检查以下几点：

1. **PR 目标是否正确？**
   - 应该是 `westlakeomics/Python_Git_exam:main`
   - 不是你的 Fork 的 main

2. **是否修改了正确的文件？**
   - CI 触发器监控 `src/**.py`、`tests/**.py` 等
   - 确认你修改了 `src/bad_style.py`

3. **Fork 的 Actions 是否启用？**
   - 访问你的 Fork 的 Actions 标签页
   - 如果被禁用，点击 "Enable Actions"

### Q9: 为什么推送后没有自动创建 PR？

**A:** 自动 PR 创建（`.github/workflows/auto-pr.yml`）仅在**直接推送到原仓库**时触发。

**Fork 工作流需要手动创建 PR：**
- Fork 推送 → 你的 Fork 的分支
- 需要跨仓库创建 PR
- 必须手动操作（GitHub 网页）

**协作者直接推送才会自动创建 PR：**
- 协作者推送 → 原仓库的分支
- 触发 `auto-pr.yml`
- 自动创建 PR

---

## 📊 对比总结

### 工作流对比

| 方面 | Fork 工作流 | 协作者工作流 |
|-----|-----------|------------|
| **权限要求** | 无 | 需要协作者权限 |
| **推送目标** | 自己的 Fork | 原仓库分支 |
| **PR 创建** | 手动（跨仓库） | 自动 |
| **CI 运行位置** | 原仓库（PR 创建后） | 原仓库 |
| **分支删除** | 自动（PR 合并后） | 自动 |
| **学习价值** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **适用场景** | 外部贡献、开源项目 | 团队协作 |

---

## 🎓 实战检查清单

### 使用 Fork 工作流前的检查

- [ ] 已在 GitHub 网页 Fork 仓库
- [ ] 克隆的是**我自己的 Fork**（不是原仓库）
- [ ] 已添加 `upstream` 远程仓库
- [ ] 已创建特性分支（不在 main 工作）
- [ ] 已在本地测试通过
- [ ] 推送到 `origin`（我的 Fork）
- [ ] 已创建跨仓库 PR

### 推送前的检查

- [ ] 代码已本地测试: `pytest tests/`
- [ ] 已运行 linter: `ruff check .`
- [ ] 已检查格式: `black --check .`
- [ ] 已检查导入: `isort --check-only .`
- [ ] 提交信息清晰描述性
- [ ] 确认推送到正确的远程和分支

---

## 🆘 获取帮助

如果以上内容无法解决你的问题：

1. **阅读完整文档**
   - [README.md](README.md) - 项目概述
   - [CONTRIBUTING.md](CONTRIBUTING.md) - 详细贡献指南

2. **搜索现有 Issues**
   - https://github.com/westlakeomics/Python_Git_exam/issues
   - 可能有人遇到了相同问题

3. **创建新 Issue**
   - 详细描述问题
   - 包含完整的错误消息
   - 说明你已尝试的步骤
   - 提供环境信息（OS、Git 版本等）

4. **联系管理员**
   - 通过项目指定的沟通渠道
   - Email 或其他联系方式

---

## 📚 扩展阅读

### GitHub 官方文档

- [About forks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks)
- [Creating a pull request from a fork](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork)
- [Access permissions on GitHub](https://docs.github.com/en/get-started/learning-about-github/access-permissions-on-github)
- [SSH authentication](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

### Git 学习资源

- [Pro Git 中文版](https://git-scm.com/book/zh/v2) - 免费在线书籍
- [Git 官方教程](https://git-scm.com/docs/gittutorial)
- [Learn Git Branching](https://learngitbranching.js.org/?locale=zh_CN) - 交互式教程

---

**文档版本**: 1.0  
**最后更新**: 2025-12-25  
**适用项目**: westlakeomics/Python_Git_exam
