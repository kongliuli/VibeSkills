---
name: git-workflow-assistant
description: "C#/Python Git工作流助手技能：负责.gitignore配置、提交信息规范、分支策略建议、PR模板生成、代码审查清单。使用时需要配置.gitignore、制定提交规范、建议分支策略、生成PR模板、创建代码审查清单。"
---

# git-workflow-assistant Skill

为C#/Python项目提供专业的Git工作流管理和配置能力，确保项目具有规范的版本控制流程和良好的团队协作实践。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置或优化.gitignore文件
- 需要制定提交信息规范
- 需要建议分支策略
- 需要生成PR模板
- 需要创建代码审查清单
- 需要优化Git工作流程

## Not For / Boundaries

- 不负责Git命令的基础操作教学
- 不替代开发人员进行代码审查
- 不处理Git服务器的配置和管理
- 不负责解决Git冲突（仅提供冲突解决策略建议）

## Quick Reference

### Common Patterns

**Pattern 1:** .gitignore配置示例
```gitignore
# Visual Studio
.vs/
*.suo
*.user
*.sln.docstates

# Build results
[Dd]ebug/
[Dd]ebugPublic/
[Rr]elease/
[Rr]eleases/

# NuGet
*.nupkg
.nuget/
packages/

# Python
__pycache__/
*.py[cod]
*.so
.Python
env/
venv/
.env

# IDE
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Logs
logs/
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Testing
coverage/

# Build tools
node_modules/
```

**Pattern 2:** 提交信息规范（Conventional Commits）
```
<类型>[可选的作用域]: <描述>

[可选的正文]

[可选的页脚]
```

类型包括：
- feat: 新功能
- fix: 修复bug
- docs: 文档变更
- style: 代码格式变更（不影响功能）
- refactor: 代码重构（不影响功能）
- perf: 性能优化
- test: 添加或修改测试
- build: 构建系统或依赖变更
- ci: CI配置文件或脚本变更
- chore: 其他不影响代码库的变更
- revert: 回退之前的提交

**Pattern 3:** 分支策略建议
```
main/master - 主分支（生产环境）
dev/develop - 开发分支（集成分支）
feature/xxx - 功能分支
bugfix/xxx - Bug修复分支
hotfix/xxx - 热修复分支
release/xxx - 发布分支
```

**Pattern 4:** PR模板
```markdown
# 拉取请求（PR）

## 描述

请简要描述此次PR的内容和目的。

## 变更类型

- [ ] 新功能（feature）
- [ ] Bug修复（fix）
- [ ] 文档更新（docs）
- [ ] 代码风格（style）
- [ ] 代码重构（refactor）
- [ ] 性能优化（perf）
- [ ] 测试（test）
- [ ] 构建配置（build）
- [ ] CI/CD（ci）
- [ ] 其他（chore）

## 相关Issue

请链接相关的Issue（如果有）：

## 测试情况

请描述测试情况（单元测试、集成测试等）：

## 截图/演示（可选）

如果有UI变更，请提供截图或演示。

## 检查清单

- [ ] 代码符合项目的编码规范
- [ ] 所有测试通过
- [ ] 文档已更新
- [ ] 提交信息符合规范
```

**Pattern 5:** 代码审查清单
```markdown
# 代码审查清单

## 功能正确性
- [ ] 代码实现了预期的功能
- [ ] 处理了边界情况
- [ ] 错误处理完善

## 代码质量
- [ ] 代码符合编码规范
- [ ] 命名规范一致
- [ ] 代码可读性好
- [ ] 没有重复代码
- [ ] 注释清晰准确

## 性能
- [ ] 没有明显的性能问题
- [ ] 资源使用合理

## 安全
- [ ] 没有安全漏洞
- [ ] 敏感信息处理正确

## 测试
- [ ] 测试覆盖全面
- [ ] 测试用例合理

## 其他
- [ ] 文档已更新
- [ ] 依赖变更合理
- [ ] 没有破坏现有功能
```

## Examples

### Example 1: 配置.gitignore文件
- Input: 需要为一个C#和Python混合项目配置.gitignore文件
- Steps:
  1. 分析项目结构和使用的工具
  2. 添加Visual Studio相关的忽略规则
  3. 添加Python相关的忽略规则
  4. 添加操作系统和IDE相关的忽略规则
  5. 添加构建和测试相关的忽略规则
- Expected output / acceptance: 完整的.gitignore文件，包含所有必要的忽略规则

### Example 2: 制定提交信息规范
- Input: 需要为团队制定Git提交信息规范
- Steps:
  1. 介绍Conventional Commits规范
  2. 定义适合团队的提交类型
  3. 提供提交信息示例
  4. 建议使用工具辅助规范执行
- Expected output / acceptance: 清晰的提交信息规范文档，包含类型定义和示例

### Example 3: 建议分支策略
- Input: 需要为一个新项目建议Git分支策略
- Steps:
  1. 分析项目规模和团队结构
  2. 建议适合的分支模型（如Git Flow、GitHub Flow等）
  3. 定义分支命名规范
  4. 描述分支合并流程
- Expected output / acceptance: 完整的分支策略建议，包含分支类型、命名规范和合并流程

## References

- `references/index.md`: Git工作流最佳实践导航
- `references/gitignore-templates.md`: .gitignore模板
- `references/commit-conventions.md`: 提交信息规范
- `references/branch-strategies.md`: 分支策略指南
- `references/pr-templates.md`: PR模板
- `references/code-review-checklists.md`: 代码审查清单

## Maintenance

- Sources: Git官方文档和行业最佳实践
- Last updated: 2026-01-20
- Known limits: 不支持高级Git配置和定制化工作流开发