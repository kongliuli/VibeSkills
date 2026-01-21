# 测试生成器 (test-generator)

为C#/Python项目提供专业的测试生成和管理能力，确保项目具有高质量的测试覆盖和良好的测试结构。

## 主要功能

- **单元测试骨架生成**：为代码自动生成单元测试骨架
- **Mock对象创建**：创建Mock对象模拟外部依赖
- **测试数据构造**：生成和构造测试数据
- **边界条件覆盖**：确保测试覆盖边界条件
- **测试项目结构管理**：管理和优化测试项目结构

## 使用场景

- 需要为代码生成单元测试骨架
- 需要创建Mock对象模拟依赖
- 需要构造测试数据
- 需要覆盖边界条件
- 需要管理测试项目结构
- 需要优化现有测试用例

## 快速开始

### C# XUnit测试骨架
```csharp
using Xunit;
using YourNamespace;

namespace YourNamespace.Tests
{
    public class CalculatorTests
    {
        [Fact]
        public void Add_ShouldReturnSumOfTwoNumbers()
        {
            // Arrange
            int a = 1;
            int b = 2;
            int expected = 3;
            var calculator = new Calculator();

            // Act
            int result = calculator.Add(a, b);

            // Assert
            Assert.Equal(expected, result);
        }
    }
}
```

### Python pytest测试
```python
import pytest
from your_module import Calculator

def test_add_should_return_sum_of_two_numbers():
    # Arrange
    calculator = Calculator()
    a = 1
    b = 2
    expected = 3

    # Act
    result = calculator.add(a, b)

    # Assert
    assert result == expected
```

### Mock对象示例 (C# Moq)
```csharp
using Moq;

// 创建Mock对象
var mockDependency = new Mock<IDependency>();

// 设置Mock行为
mockDependency.Setup(x => x.GetData()).Returns("test data");

// 使用Mock对象
var service = new Service(mockDependency.Object);
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件