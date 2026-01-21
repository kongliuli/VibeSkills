# Python类型提示师

## 技能描述

**Python类型提示师**是一个专为Python项目设计的专业技能，专注于提供高质量的类型提示和类型检查解决方案。该技能涵盖了typing模块使用、Pydantic模型设计、类型检查配置(mypy)、类型别名和泛型类型等核心领域，帮助开发人员构建类型安全、可读性高的Python代码。

### 专注领域
- **typing模块使用**：使用Python标准库的typing模块进行类型提示
- **Pydantic模型设计**：设计和使用Pydantic模型进行数据验证和序列化
- **类型检查配置**：配置和使用mypy进行静态类型检查
- **类型别名**：使用类型别名简化复杂的类型标注
- **泛型类型**：设计和使用泛型类型提高代码复用性

## 目录结构

```plaintext
skills/Python/type-hint-expert/
├── SKILL.md          # 技能技术规范文件
├── README.md         # 中文文档
├── references/       # 参考资料
├── scripts/          # 相关脚本
└── assets/           # 资源文件
```

## 快速开始

### 基本使用

1. **使用typing模块**
   - 导入必要的类型
   - 为函数参数和返回值添加类型提示
   - 为变量添加类型提示

2. **设计Pydantic模型**
   - 创建基础模型
   - 设计扩展模型
   - 添加验证器
   - 使用模型进行数据验证

3. **配置mypy**
   - 创建mypy配置文件
   - 运行mypy进行类型检查
   - 修复类型错误

4. **使用类型别名**
   - 定义类型别名
   - 在代码中使用类型别名

5. **设计泛型类型**
   - 定义类型变量
   - 设计泛型类和函数
   - 使用泛型类型

## 使用示例

### 示例1：typing模块基本使用

```python
from typing import List, Dict, Tuple, Set, Optional, Union

# 函数参数和返回值类型提示
def greet(name: str) -> str:
    """问候函数"""
    return f"Hello, {name}!"

# 复杂类型提示
def process_data(data: List[Dict[str, Union[str, int]]]) -> Tuple[bool, Optional[str]]:
    """处理数据函数"""
    if not data:
        return False, "数据为空"
    return True, None

# 类中的类型提示
class User:
    """用户类"""
    def __init__(self, name: str, age: int, email: Optional[str] = None):
        self.name: str = name
        self.age: int = age
        self.email: Optional[str] = email
    
    def get_info(self) -> Dict[str, Union[str, int, None]]:
        """获取用户信息"""
        return {
            "name": self.name,
            "age": self.age,
            "email": self.email
        }
```

### 示例2：Pydantic模型设计

```python
from pydantic import BaseModel, Field, EmailStr, validator
from typing import Optional, List
from datetime import datetime

# 基本模型
class UserBase(BaseModel):
    """用户基础模型"""
    name: str
    email: EmailStr
    age: int = Field(..., ge=0, le=150)
    is_active: bool = True

# 扩展模型
class UserCreate(UserBase):
    """用户创建模型"""
    password: str = Field(..., min_length=8)
    
    @validator('password')
    def password_strength(cls, v):
        if not any(char.isdigit() for char in v):
            raise ValueError('密码必须包含至少一个数字')
        if not any(char.isupper() for char in v):
            raise ValueError('密码必须包含至少一个大写字母')
        return v

# 用户响应模型
class User(UserBase):
    """用户响应模型"""
    id: int
    created_at: datetime
    
    class Config:
        orm_mode = True

# 使用示例
try:
    user_data = UserCreate(
        name="张三",
        email="zhangsan@example.com",
        age=30,
        password="Password123"
    )
    print("用户数据验证成功:", user_data)
except Exception as e:
    print("错误:", e)
```

### 示例3：mypy配置和使用

```ini
# mypy.ini配置文件
[mypy]
python_version = 3.10
disallow_untyped_defs = True
disallow_incomplete_defs = True
disallow_untyped_calls = True
check_untyped_defs = True
no_implicit_optional = True
warn_redundant_casts = True
warn_unused_ignores = True
warn_return_any = True
warn_unreachable = True
```

```bash
# 安装mypy
pip install mypy

# 运行mypy检查
type-hint-expert$ mypy example.py
```

### 示例4：类型别名使用

```python
from typing import TypeAlias, List, Dict, Union

# 定义类型别名
UserId: TypeAlias = int
ProductId: TypeAlias = int
Price: TypeAlias = float
Quantity: TypeAlias = int

# 复杂类型别名
OrderItem: TypeAlias = Dict[str, Union[ProductId, Price, Quantity]]
Order: TypeAlias = Dict[str, Union[UserId, List[OrderItem], Price]]

# 使用类型别名
def create_order_item(product_id: ProductId, price: Price, quantity: Quantity) -> OrderItem:
    """创建订单项"""
    return {
        "product_id": product_id,
        "price": price,
        "quantity": quantity
    }

def create_order(user_id: UserId, items: List[OrderItem]) -> Order:
    """创建订单"""
    total: Price = sum(item["price"] * item["quantity"] for item in items)
    return {
        "user_id": user_id,
        "items": items,
        "total": total
    }
```

### 示例5：泛型类型设计

```python
from typing import TypeVar, Generic, List, Optional

# 定义类型变量
T = TypeVar('T')

# 设计泛型类
class Stack(Generic[T]):
    """泛型栈类"""
    def __init__(self):
        self.items: List[T] = []
    
    def push(self, item: T) -> None:
        """入栈"""
        self.items.append(item)
    
    def pop(self) -> Optional[T]:
        """出栈"""
        if not self.items:
            return None
        return self.items.pop()
    
    def peek(self) -> Optional[T]:
        """查看栈顶元素"""
        if not self.items:
            return None
        return self.items[-1]

# 使用泛型类
# 字符串栈
string_stack = Stack[str]()
string_stack.push("Hello")
string_stack.push("World")
print(string_stack.pop())  # 输出: World

# 整数栈
int_stack = Stack[int]()
int_stack.push(1)
int_stack.push(2)
print(int_stack.pop())  # 输出: 2
```

## 最佳实践

1. **一致的类型提示**
   - 为所有公共函数和方法添加类型提示
   - 为重要的变量添加类型提示
   - 保持类型提示的一致性

2. **合理使用Pydantic**
   - 使用Pydantic进行数据验证和序列化
   - 设计清晰的模型层次结构
   - 合理使用验证器和字段约束

3. **有效的mypy配置**
   - 根据项目需求配置mypy
   - 逐步提高类型检查的严格程度
   - 定期运行mypy进行类型检查

4. **类型别名的使用**
   - 使用类型别名简化复杂的类型标注
   - 为类型别名选择有意义的名称
   - 在适当的范围内定义类型别名

5. **泛型类型的设计**
   - 使用泛型类型提高代码复用性
   - 合理定义类型变量的约束
   - 为泛型类和函数添加清晰的文档

## 常见问题

### 1. 类型提示与运行时性能

**问题**：类型提示会影响运行时性能吗？

**解决方案**：
- 类型提示在运行时会被忽略，不会影响性能
- 类型提示仅在静态类型检查时使用
- 对于大型项目，类型提示可以提高代码可读性和可维护性，间接提高开发效率

### 2. Pydantic模型与数据类

**问题**：何时使用Pydantic模型，何时使用dataclasses？

**解决方案**：
- 使用Pydantic模型进行数据验证和序列化，特别是处理外部输入时
- 使用dataclasses进行简单的数据结构，不需要验证时
- Pydantic提供了更多的验证和序列化功能

### 3. mypy类型错误处理

**问题**：如何处理mypy报告的类型错误？

**解决方案**：
- 修复实际的类型错误
- 对于无法修复的情况，使用类型忽略注释（`# type: ignore`）
- 确保类型忽略注释带有明确的原因

### 4. 类型别名的命名

**问题**：类型别名应该如何命名？

**解决方案**：
- 使用 PascalCase 命名类型别名
- 为类型别名选择描述性的名称
- 避免与内置类型和模块名冲突

### 5. 泛型类型的复杂性

**问题**：泛型类型过于复杂怎么办？

**解决方案**：
- 合理设计泛型类型，避免过度泛化
- 为复杂的泛型类型添加清晰的文档
- 考虑使用类型别名简化泛型类型的使用

## 资源链接

- [Python typing模块文档](https://docs.python.org/zh-cn/3/library/typing.html)
- [Pydantic文档](https://docs.pydantic.dev/)
- [mypy文档](https://mypy.readthedocs.io/)
- [Python类型提示最佳实践](https://realpython.com/python-type-checking/)
- [Python泛型类型指南](https://realpython.com/python-generics/)

## 总结

**Python类型提示师**技能为Python项目提供了全面的类型提示和类型检查解决方案，涵盖了从基本的typing模块使用到复杂的Pydantic模型设计和泛型类型设计的各个方面。通过遵循本技能提供的最佳实践和示例代码，开发人员可以：

- 构建类型安全的Python代码
- 提高代码的可读性和可维护性
- 减少运行时错误
- 提高开发效率
- 确保代码质量

无论是构建新的Python项目还是维护现有项目，**Python类型提示师**技能都能为开发人员提供有力的支持，帮助他们编写更加专业、可靠的Python代码。