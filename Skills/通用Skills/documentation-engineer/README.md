# 文档工程师 (documentation-engineer)

为C#/Python项目提供专业的文档编写和维护能力，确保项目具有清晰、完整、最新的文档。

## 主要功能

- **XML/Docstring注释生成**：为代码生成规范的XML或Docstring注释
- **README编写**：创建和维护项目README文件
- **API文档维护**：管理和更新API文档
- **CHANGELOG更新**：维护项目变更日志
- **接口说明文档自动化**：自动生成接口说明文档

## 使用场景

- 需要为代码生成XML或Docstring注释
- 需要编写项目README文件
- 需要维护API文档
- 需要更新CHANGELOG文件
- 需要自动化生成接口说明文档
- 需要优化现有文档结构

## 快速开始

### C# XML注释示例
```csharp
/// <summary>
/// 计算两个整数的和
/// </summary>
/// <param name="a">第一个整数</param>
/// <param name="b">第二个整数</param>
/// <returns>两个整数的和</returns>
public int Add(int a, int b)
{
    return a + b;
}
```

### Python Docstring注释示例
```python
def add(a, b):
    """
    计算两个整数的和

    Args:
        a (int): 第一个整数
        b (int): 第二个整数

    Returns:
        int: 两个整数的和
    """
    return a + b
```

### CHANGELOG示例
```markdown
# CHANGELOG

## [1.0.0] - 2026-01-20
### Added
- 初始版本发布
- 实现了核心功能

### Changed
- 优化了性能

### Fixed
- 修复了已知bug
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件