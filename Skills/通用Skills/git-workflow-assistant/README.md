# Git工作流助手 (git-workflow-assistant)

为C#/Python项目提供专业的Git工作流管理和配置能力，确保项目具有规范的版本控制流程和良好的团队协作实践。

## 主要功能

- **.gitignore配置**：创建和优化.gitignore文件
- **提交信息规范**：制定和应用提交信息规范
- **分支策略建议**：提供分支策略建议
- **PR模板生成**：生成和管理PR模板
- **代码审查清单**：创建和维护代码审查清单

## 使用场景

- 需要配置或优化.gitignore文件
- 需要制定提交信息规范
- 需要建议分支策略
- 需要生成PR模板
- 需要创建代码审查清单
- 需要优化Git工作流程

## 快速开始

### 创建.gitignore文件
```gitignore
# Visual Studio
.vs/
*.suo
*.user

# Build results
[Dd]ebug/
[Rr]elease/

# Python
__pycache__/
*.py[cod]
venv/
.env

# IDE
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db
```

### 提交信息规范示例 (Conventional Commits)
```
feat(user): 添加用户注册功能

实现了用户注册的完整流程，包括表单验证和数据库存储

Closes #123
```

### 分支策略示例
```
main/master - 主分支（生产环境）
dev/develop - 开发分支（集成分支）
feature/xxx - 功能分支
bugfix/xxx - Bug修复分支
hotfix/xxx - 热修复分支
release/xxx - 发布分支
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件