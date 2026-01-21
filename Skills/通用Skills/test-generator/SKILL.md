---
name: test-generator
description: "C#/Python测试生成器技能：负责单元测试骨架生成、Mock对象创建、测试数据构造、边界条件覆盖、测试项目结构管理。使用时需要生成测试骨架、创建Mock对象、构造测试数据、覆盖边界条件、管理测试项目结构。"
---

# test-generator Skill

为C#/Python项目提供专业的测试生成和管理能力，确保项目具有高质量的测试覆盖和良好的测试结构。

## When to Use This Skill

Trigger when any of these applies:
- 需要为代码生成单元测试骨架
- 需要创建Mock对象模拟依赖
- 需要构造测试数据
- 需要覆盖边界条件
- 需要管理测试项目结构
- 需要优化现有测试用例

## Not For / Boundaries

- 不负责编写完整的测试逻辑（仅提供骨架和模板）
- 不替代开发人员验证测试的准确性
- 不处理集成测试和端到端测试的复杂配置
- 不负责测试的执行和结果分析

## Quick Reference

### Common Patterns

**Pattern 1:** C# XUnit测试骨架
```csharp
using Xunit;
using Moq;
using System;
using YourNamespace;

namespace YourNamespace.Tests
{
    public class CalculatorTests
    {
        private readonly Calculator _calculator;
        private readonly Mock<IDependency> _mockDependency;

        public CalculatorTests()
        {
            _mockDependency = new Mock<IDependency>();
            _calculator = new Calculator(_mockDependency.Object);
        }

        [Fact]
        public void Add_ShouldReturnSumOfTwoNumbers()
        {
            // Arrange
            int a = 1;
            int b = 2;
            int expected = 3;

            // Act
            int result = _calculator.Add(a, b);

            // Assert
            Assert.Equal(expected, result);
        }

        [Theory]
        [InlineData(0, 0, 0)]
        [InlineData(-1, -1, -2)]
        [InlineData(int.MaxValue, 1, int.MinValue)]  // 边界条件
        public void Add_ShouldHandleVariousInputs(int a, int b, int expected)
        {
            // Act
            int result = _calculator.Add(a, b);

            // Assert
            Assert.Equal(expected, result);
        }
    }
}
```

**Pattern 2:** Python pytest测试骨架
```python
import pytest
from unittest.mock import Mock, patch
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

@pytest.mark.parametrize("a, b, expected", [
    (0, 0, 0),
    (-1, -1, -2),
    (999999, 1, 1000000),  # 边界条件
])
def test_add_should_handle_various_inputs(a, b, expected):
    # Arrange
    calculator = Calculator()

    # Act
    result = calculator.add(a, b)

    # Assert
    assert result == expected

def test_add_with_mocked_dependency():
    # Arrange
    mock_dependency = Mock()
    mock_dependency.get_factor.return_value = 2
    calculator = Calculator(dependency=mock_dependency)
    a = 1
    b = 2

    # Act
    result = calculator.add_with_factor(a, b)

    # Assert
    assert result == 6  # (1+2)*2
    mock_dependency.get_factor.assert_called_once()
```

**Pattern 3:** Mock对象创建 (C# Moq)
```csharp
// 创建Mock对象
var mockRepository = new Mock<IRepository>();

// 设置Mock行为
mockRepository.Setup(x => x.GetById(1)).Returns(new Entity { Id = 1, Name = "Test" });
mockRepository.Setup(x => x.GetById(It.IsAny<int>())).Throws<NotFoundException>();

// 验证Mock调用
mockRepository.Verify(x => x.Save(It.IsAny<Entity>()), Times.Once);
```

**Pattern 4:** Mock对象创建 (Python unittest.mock)
```python
# 创建Mock对象
mock_repository = Mock()

# 设置Mock行为
mock_repository.get_by_id.return_value = {"id": 1, "name": "Test"}
mock_repository.get_by_id.side_effect = NotFoundException()

# 验证Mock调用
mock_repository.save.assert_called_once()
mock_repository.save.assert_called_with({"id": 1, "name": "Test"})
```

## Examples

### Example 1: 为C#类生成XUnit测试骨架
- Input: 需要为一个C# Calculator类生成XUnit测试骨架
- Steps:
  1. 分析Calculator类的结构和方法
  2. 生成测试类和测试方法骨架
  3. 添加必要的Mock对象设置
  4. 包含边界条件测试用例
- Expected output / acceptance: 完整的XUnit测试骨架，包含所有必要的测试方法和Mock设置

### Example 2: 为Python函数创建pytest测试
- Input: 需要为一个Python函数创建pytest测试
- Steps:
  1. 分析函数的输入和输出
  2. 创建测试函数和参数化测试用例
  3. 添加Mock对象模拟外部依赖
  4. 覆盖正常和异常情况
- Expected output / acceptance: 完整的pytest测试，包含参数化测试和Mock设置

### Example 3: 构造测试数据
- Input: 需要为一个用户管理系统构造测试数据
- Steps:
  1. 分析数据模型和验证规则
  2. 创建正常、边界、异常情况下的测试数据
  3. 使用数据生成器或工厂模式
  4. 确保数据覆盖所有测试场景
- Expected output / acceptance: 完整的测试数据集，覆盖所有测试场景

## References

- `references/index.md`: 测试生成最佳实践导航
- `references/csharp-testing.md`: C#测试指南
- `references/python-testing.md`: Python测试指南
- `references/mock-objects.md`: Mock对象创建指南
- `references/test-data.md`: 测试数据构造指南

## Maintenance

- Sources: 官方测试框架文档和行业最佳实践
- Last updated: 2026-01-20
- Known limits: 不支持复杂的测试框架配置和高级Mock场景