# 项目架构师 (project-architect)

为C#/Python项目提供专业的架构设计和项目结构管理能力，确保项目具有良好的可维护性和扩展性。

## 主要功能

- **项目结构生成**：创建符合行业最佳实践的项目目录结构
- **目录规范**：制定清晰的目录组织规范
- **解决方案文件管理**：配置和管理.sln/.pyproj等解决方案文件
- **项目模板初始化**：快速初始化项目模板
- **多项目依赖配置**：管理和配置多个项目之间的依赖关系

## 使用场景

- 需要创建新的C#或Python项目结构
- 需要优化现有项目的目录组织
- 需要配置解决方案文件
- 需要初始化项目模板
- 需要管理多项目间的依赖关系
- 需要制定项目架构规范

## 快速开始

### C#项目示例
```bash
dotnet new sln -n MySolution
dotnet new classlib -n MyProject -o src/MyProject
dotnet sln add src/MyProject/MyProject.csproj
dotnet new xunit -n MyProject.Tests -o tests/MyProject.Tests
dotnet add tests/MyProject.Tests/MyProject.Tests.csproj reference src/MyProject/MyProject.csproj
dotnet sln add tests/MyProject.Tests/MyProject.Tests.csproj
```

### Python项目示例
```bash
mkdir -p my_project/src/my_project my_project/tests my_project/docs
cd my_project
python -m venv venv
pip install pytest
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件