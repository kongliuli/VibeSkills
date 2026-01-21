# 代码规范审查员 (code-style-reviewer)

为C#/Python项目提供专业的代码规范审查和格式检查能力，确保项目代码符合行业最佳实践和团队约定。

## 主要功能

- **编码规范检查**：验证代码是否符合行业和团队编码规范
- **命名约定验证**：检查变量、方法、类等命名是否符合规范
- **格式化配置**：配置和管理EditorConfig等格式化规则
- **代码风格一致性**：确保整个项目代码风格一致
- **静态代码分析规则**：配置和应用静态代码分析规则

## 使用场景

- 需要检查代码是否符合编码规范
- 需要验证命名约定是否正确
- 需要配置EditorConfig等格式化规则
- 需要确保代码风格一致性
- 需要配置静态代码分析规则
- 需要审查现有代码的规范问题

## 快速开始

### 创建EditorConfig配置
```ini
# EditorConfig is awesome: https://EditorConfig.org
root = true

[*]
end_of_line = lf
insert_final_newline = true
charset = utf-8

[*.cs]
indent_style = space
indent_size = 4

[*.py]
indent_style = space
indent_size = 4
```

### C#代码格式化
```bash
# 安装dotnet format
dotnet tool install -g dotnet-format

# 运行代码格式化
dotnet format
```

### Python代码风格检查
```bash
# 安装Flake8
pip install flake8

# 运行代码风格检查
flake8 .
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件