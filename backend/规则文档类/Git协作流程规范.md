# Git协作流程规范文档

## 1. 概述

本文档旨在规范联通运营商后台管理系统项目的Git协作流程，确保团队成员能够高效协作，保证代码质量和项目进度。本规范适用于项目中的所有开发人员。

## 2. Git分支策略

### 2.1 分支类型

#### 2.1.1 主分支（main/master）
- 用途：生产环境代码，始终保持稳定可发布状态
- 保护：禁止直接提交，只能通过合并其他分支更新
- 命名：main 或 master
- 操作：仅用于发布版本和紧急修复

#### 2.1.2 开发分支（develop）
- 用途：开发环境代码，集成最新功能
- 保护：禁止直接提交，只能通过合并其他分支更新
- 命名：develop
- 操作：功能开发完成后合并到此分支

#### 2.1.3 功能分支（feature）
- 用途：开发新功能或模块
- 来源：从develop分支创建
- 命名：feature/功能描述
- 操作：开发完成后合并回develop分支
- 示例：
  ```
  feature/user-management
  feature/plan-management
  feature/report-analysis
  ```

#### 2.1.4 发布分支（release）
- 用途：准备发布新版本，进行测试和修复
- 来源：从develop分支创建
- 命名：release/版本号
- 操作：测试完成后合并到main和develop分支
- 示例：
  ```
  release/v1.0.0
  release/v1.1.0
  ```

#### 2.1.5 修复分支（hotfix）
- 用途：修复生产环境紧急问题
- 来源：从main分支创建
- 命名：hotfix/问题描述
- 操作：修复完成后合并到main和develop分支
- 示例：
  ```
  hotfix/login-bug
  hotfix/payment-issue
  ```

### 2.2 分支流程图

```
main (生产)
  ↑
hotfix (紧急修复)
  ↑
release (发布准备)
  ↑
develop (开发)
  ↑
feature (功能开发)
```

## 3. 提交规范

### 3.1 提交信息格式

提交信息采用以下格式：
```
<类型>(<范围>): <主题>

<详细描述>

<关闭的Issue>
```

#### 3.1.1 类型（type）
- feat：新功能
- fix：修复bug
- docs：文档更新
- style：代码格式调整（不影响功能）
- refactor：代码重构（既不是新增功能，也不是修改bug）
- perf：性能优化
- test：测试相关
- chore：构建过程或辅助工具的变动
- revert：回滚之前的提交

#### 3.1.2 范围（scope）
- 指明提交影响的范围，可以是模块名、文件名等
- 如果影响多个范围，可以用逗号分隔
- 示例：user, plan, report

#### 3.1.3 主题（subject）
- 简短描述提交内容，不超过50个字符
- 使用祈使语气，如："add"、"update"、"fix"等
- 首字母小写，结尾不加句号

#### 3.1.4 详细描述（body）
- 详细描述提交的内容和原因
- 可以分多行，每行不超过72个字符
- 解释代码的变更和设计决策

#### 3.1.5 关闭的Issue（footer）
- 如果提交解决了某个Issue，使用以下格式：
  ```
  Closes #123
  Fixes #456
  Resolves #789
  ```

### 3.2 提交信息示例

#### 3.2.1 新功能
```
feat(user): 添加用户管理功能

- 实现用户列表查询功能
- 添加用户创建和编辑功能
- 实现用户状态管理

Closes #123
```

#### 3.2.2 修复bug
```
fix(plan): 修复套餐价格计算错误

修复了套餐折扣计算时未考虑优惠活动的问题，
导致部分套餐价格计算错误。

Fixes #456
```

#### 3.2.3 文档更新
```
docs(api): 更新API文档

添加了用户管理相关API的文档，
包括请求参数和响应格式说明。
```

#### 3.2.4 代码重构
```
refactor(auth): 重构认证模块

- 抽取公共认证逻辑
- 优化JWT令牌生成和验证
- 简化权限检查流程
```

## 4. 工作流程

### 4.1 功能开发流程

1. **创建功能分支**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/user-management
   ```

2. **开发功能**
   - 按照代码规范编写代码
   - 定期提交代码，遵循提交规范
   - 确保代码质量和测试覆盖率

3. **提交代码**
   ```bash
   git add .
   git commit -m "feat(user): 添加用户管理功能"
   git push origin feature/user-management
   ```

4. **创建Pull Request**
   - 在GitLab/GitHub上创建PR
   - 填写PR描述，包括功能说明和测试情况
   - 指定代码审查者

5. **代码审查**
   - 审查者检查代码质量、逻辑正确性
   - 提出修改建议
   - 开发者根据建议修改代码

6. **合并代码**
   - 审查通过后合并到develop分支
   - 删除功能分支

### 4.2 发布流程

1. **创建发布分支**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b release/v1.0.0
   ```

2. **版本准备**
   - 更新版本号
   - 更新CHANGELOG
   - 进行测试和修复

3. **提交发布分支**
   ```bash
   git add .
   git commit -m "chore(release): 准备v1.0.0版本发布"
   git push origin release/v1.0.0
   ```

4. **合并到主分支**
   ```bash
   git checkout main
   git pull origin main
   git merge --no-ff release/v1.0.0
   git tag -a v1.0.0 -m "发布v1.0.0版本"
   git push origin main --tags
   ```

5. **合并到开发分支**
   ```bash
   git checkout develop
   git pull origin develop
   git merge --no-ff release/v1.0.0
   git push origin develop
   ```

6. **删除发布分支**
   ```bash
   git branch -d release/v1.0.0
   git push origin --delete release/v1.0.0
   ```

### 4.3 紧急修复流程

1. **创建修复分支**
   ```bash
   git checkout main
   git pull origin main
   git checkout -b hotfix/login-bug
   ```

2. **修复问题**
   - 定位并修复问题
   - 添加测试用例
   - 确保修复有效

3. **提交修复**
   ```bash
   git add .
   git commit -m "fix(auth): 修复登录验证错误"
   git push origin hotfix/login-bug
   ```

4. **合并到主分支**
   ```bash
   git checkout main
   git pull origin main
   git merge --no-ff hotfix/login-bug
   git tag -a v1.0.1 -m "紧急修复v1.0.1版本"
   git push origin main --tags
   ```

5. **合并到开发分支**
   ```bash
   git checkout develop
   git pull origin develop
   git merge --no-ff hotfix/login-bug
   git push origin develop
   ```

6. **删除修复分支**
   ```bash
   git branch -d hotfix/login-bug
   git push origin --delete hotfix/login-bug
   ```

## 5. Pull Request规范

### 5.1 PR标题
- 遵循提交信息格式
- 简洁明了地描述PR内容
- 示例：`feat(user): 添加用户管理功能`

### 5.2 PR描述
#### 5.2.1 描述模板
```markdown
## 变更内容
- 简要描述变更内容

## 变更原因
- 说明为什么需要这些变更

## 测试情况
- 描述测试方法和结果
- 说明测试覆盖率

## 相关Issue
- 关联的Issue编号

## 截图（如适用）
- 添加相关截图

## 检查清单
- [ ] 代码已通过静态检查
- [ ] 单元测试已通过
- [ ] 集成测试已通过
- [ ] 代码已通过审查
```

#### 5.2.2 描述示例
```markdown
## 变更内容
- 实现用户列表查询功能
- 添加用户创建和编辑功能
- 实现用户状态管理

## 变更原因
为了满足用户管理需求，需要提供完整的用户管理功能，包括用户查询、创建、编辑和状态管理。

## 测试情况
- 单元测试覆盖率85%
- 集成测试通过
- 功能测试通过

## 相关Issue
Closes #123

## 截图
![用户列表](screenshots/user-list.png)
![用户编辑](screenshots/user-edit.png)

## 检查清单
- [x] 代码已通过静态检查
- [x] 单元测试已通过
- [x] 集成测试已通过
- [ ] 代码已通过审查
```

### 5.3 PR审查流程
1. **自检**
   - 提交前进行自我检查
   - 确保代码符合规范
   - 确保测试通过

2. **创建PR**
   - 填写完整的PR描述
   - 指定合适的审查者
   - 设置相关的标签

3. **代码审查**
   - 审查者检查代码质量
   - 提出修改建议
   - 讨论技术方案

4. **修改代码**
   - 根据审查意见修改代码
   - 回应审查问题
   - 更新PR描述

5. **合并PR**
   - 审查通过后合并
   - 使用 squash merge
   - 删除源分支

## 6. 代码审查规范

### 6.1 审查者职责
- 检查代码是否符合项目规范
- 检查逻辑是否正确
- 检查是否有潜在的安全问题
- 检查性能是否有优化空间
- 提供建设性的反馈和建议

### 6.2 审查要点
- **代码质量**
  - 是否符合编码规范
  - 是否易于理解和维护
  - 是否有足够的注释

- **逻辑正确性**
  - 业务逻辑是否正确
  - 是否有潜在的bug
  - 边界条件是否处理

- **安全性**
  - 是否有安全漏洞
  - 输入验证是否充分
  - 权限控制是否合理

- **性能**
  - 是否有性能问题
  - 数据库查询是否优化
  - 是否有内存泄漏风险

- **测试**
  - 是否有足够的测试
  - 测试用例是否覆盖主要场景
  - 测试是否可维护

### 6.3 审查反馈
- 反馈应具体、明确
- 提供改进建议
- 保持友好和专业的态度
- 及时响应和讨论

## 7. Git配置规范

### 7.1 全局配置
```bash
git config --global user.name "姓名"
git config --global user.email "邮箱"
git config --global core.autocrlf input  # Mac/Linux
git config --global core.autocrlf true   # Windows
git config --global core.editor "vim"    # 设置默认编辑器
```

### 7.2 项目配置
```bash
git config core.filemode false           # 忽略文件权限变化
git config pull.rebase false             # pull时使用merge而非rebase
```

### 7.3 别名配置
```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
```

## 8. Git忽略文件规范

### 8.1 全局忽略文件
创建 `~/.gitignore_global` 文件：
```
# 操作系统生成的文件
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# IDE和编辑器文件
.vscode/
.idea/
*.swp
*.swo
*~

# 日志文件
*.log
logs/

# 临时文件
*.tmp
*.temp
```

配置全局忽略文件：
```bash
git config --global core.excludesfile ~/.gitignore_global
```

### 8.2 项目忽略文件
项目根目录下的 `.gitignore` 文件：
```
# 依赖目录
node_modules/
target/
dist/
build/

# 环境配置文件
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# 日志文件
logs/
*.log

# 运行时数据
pids/
*.pid
*.seed
*.pid.lock

# 覆盖率报告
coverage/
.nyc_output/

# 依赖锁定文件
package-lock.json
yarn.lock

# 编译输出
*.class
*.exe
*.dll
*.so
*.dylib

# 测试输出
test-results/
```

## 9. 版本管理规范

### 9.1 版本号格式
采用语义化版本号（Semantic Versioning）：
```
主版本号.次版本号.修订号
```

- **主版本号**：不兼容的API修改
- **次版本号**：向下兼容的功能性新增
- **修订号**：向下兼容的问题修正

### 9.2 版本发布策略
- **主版本**：重大架构变更、不兼容的API修改
- **次版本**：新功能发布、重要功能改进
- **修订版本**：bug修复、小改进

### 9.3 版本标签
- 使用Git标签标记版本
- 标签名与版本号一致
- 添加标签描述
  ```bash
  git tag -a v1.0.0 -m "发布v1.0.0版本"
  git push origin v1.0.0
  ```

## 10. 常见问题与解决方案

### 10.1 合并冲突
#### 问题描述
在合并分支时出现冲突

#### 解决方案
1. **拉取最新代码**
   ```bash
   git fetch origin
   git rebase origin/develop
   ```

2. **解决冲突**
   - 手动编辑冲突文件
   - 保留需要的代码
   - 删除冲突标记

3. **标记冲突已解决**
   ```bash
   git add <冲突文件>
   git rebase --continue
   ```

4. **推送代码**
   ```bash
   git push origin feature/xxx
   ```

### 10.2 提交错误
#### 问题描述
提交信息写错或提交了错误的文件

#### 解决方案
1. **修改最后一次提交**
   ```bash
   git commit --amend
   ```

2. **修改多个提交**
   ```bash
   git rebase -i HEAD~n
   ```

3. **撤销提交**
   ```bash
   git reset --soft HEAD~1
   ```

### 10.3 误删分支
#### 问题描述
误删了重要分支

#### 解决方案
1. **查找分支引用**
   ```bash
   git reflog
   ```

2. **恢复分支**
   ```bash
   git checkout -b <分支名> <引用>
   ```

## 11. 工具推荐

### 11.1 Git客户端
- **SourceTree**：可视化Git客户端
- **GitKraken**：美观的Git客户端
- **GitHub Desktop**：GitHub官方客户端

### 11.2 IDE集成
- **VSCode**：内置Git支持
- **IntelliJ IDEA**：强大的Git集成
- **WebStorm**：前端开发IDE

### 11.3 命令行工具
- **Git**：官方命令行工具
- **Oh My Zsh**：增强的Shell环境
- **Git Flow**：Git工作流工具

---

文档版本：V1.0  
创建日期：2023年11月15日  
最后更新：2023年11月15日  
创建人：架构师  
审核人：技术总监