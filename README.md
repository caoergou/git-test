# 一、问题翻译（英文→中文）

1. pull 和 fetch 的区别
2. rebase 和 merge 的区别
3. revert 和 reset 的区别
4. 为什么需要不同的分支
5. 为什么在拉取请求（PR）打开时不应该强制推送到远程分支
6. 为什么不应该强制推送到远程主分支（master/main）
7. 如何删除一个提交
8. 如何创建一个分支（并切换到该分支）
9. 如何恢复暂存的更改
10. 如何比较两个分支（或提交）
11. 如何提交更改
12. 如何将未暂存的更改推送到远程分支
13. 如何显示当前更改
14. 如何显示提交日志
15. 如何将一个分支合并到另一个分支
16. 如何将一个分支变基到另一个分支
17. 描述 rebase 的作用
18. 描述 stash 的作用
19. 如何将多个提交压缩为一个提交
20. 如何在另一个分支上应用一个提交

### 二、逐题解析（含例子）

#### 1. pull 和 fetch 的区别

- **pull**：从远程仓库获取代码并自动合并到当前分支（等价于 `fetch + merge`）。
- **fetch**：仅从远程仓库获取代码，不自动合并，需要手动执行合并。

```bash
**例子**：
当团队成员推送新代码到远程分支 `feature/login` 时：

```bash
# 仅获取代码但不合并（安全操作）
git fetch origin feature/login

# 获取并自动合并（可能覆盖本地未提交的更改）
git pull origin feature/login
```

#### 2. rebase 和 merge 的区别

- **merge**：将分支A合并到分支B时，生成一个新的合并提交，保留所有分支的历史。
- **rebase**：将分支A的提交“移动”到分支B的最新提交之后，使历史更线性。

**例子**：
当前分支 `feature` 基于 `main` 分支开发，此时 `main` 有新提交：

```bash
# merge 方式（生成合并提交）
git checkout main
git merge feature

# rebase 方式（重写历史，使 feature 提交接在 main 之后）
git checkout feature
git rebase main
```

#### 3. revert 和 reset 的区别

- **revert**：创建一个新提交来撤销指定提交的更改，保留历史记录。
- **reset**：移动HEAD指针到指定提交，可能丢失未提交的更改或后续提交。

**例子**：
撤销提交 `abc123`：

```bash
# 安全撤销（推荐）：生成新提交撤销更改
git revert abc123

# 危险操作（可能丢失数据）：回退到提交并丢弃后续更改
git reset --hard abc123
```

#### 4. 为什么需要不同的分支

- **功能隔离**：不同功能在独立分支开发，避免互相干扰（如 `feature/login` 和 `feature/payment`）。
- **协作安全**：主分支（main）保持稳定，开发分支可自由修改。
- **版本管理**：便于维护多个版本（如 `release/v1.0` 分支）。
- **实验性开发**：临时分支用于测试新想法，失败可直接删除。

#### 5. 为什么PR打开时不应该强制推送

- **PR评审混乱**：强制推送（`git push --force`）会修改远程分支的历史，已评审的提交可能被覆盖，评审记录失效。
- **协作风险**：其他成员可能已基于旧历史开发，强制推送会导致他们的代码冲突。

#### 6. 为什么不应该强制推送主分支

- **历史丢失**：主分支（main）是团队共享的稳定分支，强制推送会删除其他成员的提交历史。
- **流程破坏**：违背“主分支只能通过PR合并”的最佳实践，导致版本不可追溯。

#### 7. 如何删除一个提交

- **场景1：删除最新提交（未推送到远程）**
  `git reset --hard HEAD^`（`HEAD^` 表示上一个提交）。
- **场景2：删除中间提交（已推送，需保留历史）**
  `git revert <commit-hash>`（生成新提交撤销更改）。

**例子**：删除第3个提交（假设提交历史为 A→B→C）：

```bash
# 未推送时回退到B提交
git reset --hard HEAD^

# 已推送时撤销C提交（生成D提交）
git revert C的哈希值
```

#### 8. 如何创建并切换分支

- 命令：`git switch -c <分支名>` 或 `git checkout -b <分支名>`。

**例子**：创建并切换到 `feature/login` 分支：

```bash
git switch -c feature/login
# 等价于
git branch feature/login
git switch feature/login
```

#### 9. 如何恢复暂存的更改

- 暂存更改：`git stash save "描述"`
- 恢复并删除暂存：`git stash pop`
- 恢复但保留暂存：`git stash apply`

**例子**：

```bash
# 暂存当前更改
git stash save "临时保存登录页更改"

# 恢复暂存并删除记录
git stash pop

# 恢复暂存但保留记录（可多次应用）
git stash apply stash@{0}  # stash@{0} 表示最新暂存
```

#### 10. 如何比较分支/提交

- 比较分支：`git diff <分支1> <分支2>`
- 比较提交：`git diff <commit1> <commit2>`

**例子**：比较 `feature/login` 和 `main` 分支的差异：

```bash
git diff main feature/login
```

#### 11. 如何提交更改

1. 暂存文件：`git add <文件>` 或 `git add .`
2. 提交更改：`git commit -m "提交信息"`

**例子**：

```bash
# 暂存所有修改的文件
git add .

# 提交并添加描述
git commit -m "完成登录功能开发"
```

#### 12. 如何推送未暂存的更改

- 先暂存或提交，再推送；若需快速推送未暂存更改，可使用 `git commit -a`（暂存所有已跟踪文件并提交）。

**例子**：

```bash
# 暂存所有已跟踪文件并提交
git commit -a -m "快速提交未暂存的更改"

# 推送到远程分支
git push origin feature/login
```

#### 13. 如何显示当前更改

- 查看未暂存更改：`git diff`
- 查看已暂存更改：`git diff --staged`

**例子**：

```bash
# 查看所有未暂存的文件修改
git diff

# 查看已暂存（准备提交）的文件修改
git diff --staged
```

#### 14. 如何显示提交日志

- 简洁日志：`git log --oneline`
- 图形化日志：`git log --graph --all --oneline`
- 查看某分支日志：`git log <分支名>`

**例子**：

```bash
# 查看当前分支的简洁日志
git log --oneline

# 查看所有分支的图形化日志
git log --graph --all --oneline
```

#### 15. 如何合并分支

- 合并分支B到当前分支A：`git merge <分支B>`

**例子**：将 `feature/login` 合并到 `main` 分支：

```bash
git checkout main
git merge feature/login
```

#### 16. 如何变基分支

- 将分支B变基到分支A：`git rebase <分支A>`（需先切换到分支B）

**例子**：将 `feature/login` 变基到 `main` 的最新提交：

```bash
git checkout feature/login
git rebase main
```

#### 17. rebase 的作用

- 将当前分支的提交移动到另一个分支的最新提交之后，使历史更线性（无合并提交）。
- 常用于“整理提交历史”，让分支历史更清晰（如压缩多个小提交为一个）。

#### 18. stash 的作用

- 临时保存当前工作目录的更改（未提交的修改），便于切换分支或清理工作区，之后可恢复更改。
- 场景：需要紧急处理其他任务，但当前代码未完成，可暂存后切换分支。

#### 19. 如何压缩提交

- 交互式变基：`git rebase -i HEAD~n`（`n` 为需要压缩的提交数），将提交标记为 `squash`。

**例子**：压缩最近3个提交为一个：

```bash
git rebase -i HEAD~3
# 在弹出的编辑器中，将第2、3个提交的命令改为 "squash" 或 "s"
# 保存后输入合并后的提交信息
```

#### 20. 如何在另一个分支应用提交

- 使用 `git cherry-pick <commit-hash>` 将指定提交应用到当前分支。

**例子**：将分支A的提交应用到分支B：

```bash
git checkout branchB
git cherry-pick <commit-hash-from-branchA>
```

### 三、Git 练习手册

#### 练习1：基础操作（本地仓库）

1. **初始化仓库**

   ```bash
   mkdir git-practice && cd git-practice
   git init
   ```

2. **创建文件并提交**

   ```bash
   echo "Hello Git" > README.md
   git add README.md
   git commit -m "初始化项目"
   ```

3. **创建分支并开发**

   ```bash
   git switch -c feature/hello
   echo "World" >> README.md
   git commit -m "添加 World"
   ```

4. **暂存与恢复**

   ```bash
   echo "Test" >> test.txt
   git stash save "临时文件"
   git status  # 应显示干净工作区
   git stash pop  # 恢复暂存
   ```

5. **压缩提交**

   ```bash
   git rebase -i HEAD~2  # 压缩最近2个提交
   ```

#### 练习2：分支与合并

1. **创建多个分支**

   ```bash
   git switch main
   git switch -c feature/login
   git switch -c feature/payment
   ```

2. **在不同分支开发**

   ```bash
   # 在 login 分支
   echo "登录功能" > login.txtq
   git add . && git commit -m "完成登录功能"

   # 切换到 payment 分支
   git switch payment
   echo "支付功能" > payment.txt
   git add . && git commit -m "完成支付功能"
   ```

3. **合并分支到 main**

   ```bash
   git switch main
   git merge feature/login
   git merge feature/payment
   ```

4. **变基操作**

   ```bash
   git switch feature/login
   git rebase main  # 将 login 分支变基到 main 最新提交
   ```

#### 练习3：协作与远程操作（需创建远程仓库）

1. **关联远程仓库**

   ```bash
   git remote add origin <远程仓库地址>
   ```

2. **推送分支**

   ```bash
   git switch -c feature/new-feature
   # 开发并提交...
   git push -u origin feature/new-feature
   ```

3. **模拟团队协作**

   ```bash
   # 假设远程 main 分支有新提交，拉取并合并
   git switch main
   git pull origin main

   # 或使用 rebase 保持线性历史
   git rebase origin/main
   ```

4. **处理冲突**
   在两个分支修改同一文件，合并时故意制造冲突，练习解决冲突：

   ```bash
   git switch feature/new-feature
   echo "冲突内容" >> conflict.txt
   git commit -m "制造冲突"
   git switch main
   echo "不同的冲突内容" >> conflict.txt
   git commit -m "制造冲突"
   git merge feature/new-feature  # 解决冲突后提交
   ```

#### 练习4：危险操作与恢复（建议在测试仓库执行）

1. **撤销提交（revert）**

   ```bash
   # 找到一个提交哈希
   git log --oneline
   git revert <commit-hash>  # 安全撤销
   ```

2. **回退提交（reset）**

   ```bash
   git reset --hard HEAD^  # 回退到上一个提交（未推送时使用）
   ```

3. **恢复已删除的提交**

   ```bash
   git reflog  # 查看所有HEAD历史
   git reset --hard <哈希值>  # 恢复指定状态
   ```

### 四、学习资源推荐

- **官方文档**：[Pro Git 书籍（免费）](https://git-scm.com/book/zh/v2)
- **交互式练习**：[Learn Git Branching](https://learngitbranching.js.org/)
- **可视化工具**：Sourcetree（适合新手）、GitKraken（跨平台）

通过以上练习，可逐步掌握 Git 的核心操作，建议每次练习后用 `git log --graph` 查看历史，加深对分支和合并的理解。
