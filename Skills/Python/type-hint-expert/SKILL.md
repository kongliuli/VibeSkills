---
name: python-type-hint-expert
description: "Python类型提示师技能：负责typing模块使用、Pydantic模型设计、类型检查配置(mypy)、类型别名、泛型类型。使用时需要正确使用typing模块、设计Pydantic模型、配置mypy、使用类型别名、设计泛型类型。"
---

# python-type-hint-expert Skill

为Python项目提供专业的类型提示和类型检查能力，确保代码的类型安全性和可读性，提高开发效率和代码质量。

## When to Use This Skill

Trigger when any of these applies:
- 需要使用typing模块进行类型提示
- 需要设计Pydantic模型
- 需要配置mypy进行类型检查
- 需要使用类型别名简化类型标注
- 需要设计泛型类型
- 需要优化现有代码的类型提示

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行类型设计决策
- 不处理运行时类型检查（仅静态类型提示）
- 不负责类型提示的深度性能优化

## Quick Reference

### Common Patterns

**Pattern 1:** typing模块使用
```python
# 基本类型提示
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

# 类型提示示例类
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

**Pattern 2:** Pydantic模型设计
```python
# Pydantic模型设计
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

# 嵌套模型
class Address(BaseModel):
    """地址模型"""
    street: str
    city: str
    zip_code: str

class UserWithAddress(User):
    """带地址的用户模型"""
    addresses: List[Address]
```

**Pattern 3:** 类型检查配置(mypy)
```python
# mypy配置文件 (mypy.ini)
'''
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
'''

# 示例代码：使用类型忽略注释
from typing import List, Optional

def process_items(items: List[str]) -> Optional[str]:
    """处理项目"""
    if not items:
        return None
    # 类型忽略注释示例
    result = items[0]  # type: ignore[unreachable]
    return result

# 示例代码：使用类型别名与mypy
from typing import Dict, List, TypeAlias

# 类型别名
UserId: TypeAlias = int
UserDict: TypeAlias = Dict[str, str]

# 使用类型别名
def get_user(user_id: UserId) -> UserDict:
    """获取用户"""
    return {"id": str(user_id), "name": "Test User"}
```

**Pattern 4:** 类型别名
```python
# 类型别名
from typing import TypeAlias, List, Dict, Union, Optional

# 基本类型别名
UserId: TypeAlias = int
Username: TypeAlias = str
Email: TypeAlias = str

# 复杂类型别名
UserInfo: TypeAlias = Dict[str, Union[str, int, bool]]
UserList: TypeAlias = List[UserInfo]

# 嵌套类型别名
ApiResponse: TypeAlias = Dict[str, Union[bool, str, UserList]]

# 使用类型别名
def get_user_info(user_id: UserId) -> UserInfo:
    """获取用户信息"""
    return {
        "id": user_id,
        "name": "Test User",
        "age": 30,
        "active": True
    }

def get_users() -> UserList:
    """获取用户列表"""
    return [
        get_user_info(1),
        get_user_info(2)
    ]

def create_api_response(success: bool, data: Optional[UserList] = None) -> ApiResponse:
    """创建API响应"""
    response = {
        "success": success,
        "message": "操作成功" if success else "操作失败"
    }
    if data is not None:
        response["data"] = data
    return response
```

**Pattern 5:** 泛型类型
```python
# 泛型类型
from typing import TypeVar, Generic, List, Optional

# 定义类型变量
T = TypeVar('T')
U = TypeVar('U')

# 泛型类
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
    
    def is_empty(self) -> bool:
        """判断栈是否为空"""
        return len(self.items) == 0

# 泛型函数
def first_element(items: List[T]) -> Optional[T]:
    """获取列表的第一个元素"""
    if not items:
        return None
    return items[0]

# 泛型函数与类型约束
from typing import TypeVar, Generic, List, Optional, Protocol

# 定义带约束的类型变量
class HasName(Protocol):
    """带名称的协议"""
    name: str

T = TypeVar('T', bound=HasName)

# 泛型函数与协议
def get_name(item: T) -> str:
    """获取带名称对象的名称"""
    return item.name

# 使用示例
class Person:
    """人员类"""
    def __init__(self, name: str):
        self.name = name

class Animal:
    """动物类"""
    def __init__(self, name: str, species: str):
        self.name = name
        self.species = species

# 测试泛型函数
person = Person("Alice")
animal = Animal("Bob", "Dog")

print(get_name(person))  # 输出: Alice
print(get_name(animal))  # 输出: Bob
```

## Examples

### Example 1: 基本类型提示

**Scenario:** 为函数添加类型提示，提高代码可读性和类型安全性。

**Steps:**
1. 导入必要的类型
2. 为函数参数添加类型提示
3. 为函数返回值添加类型提示
4. 为变量添加类型提示

**Code:**
```python
from typing import List, Dict, Optional

def calculate_total(items: List[Dict[str, float]]) -> float:
    """计算总金额"""
    total: float = 0.0
    for item in items:
        price: float = item.get("price", 0.0)
        quantity: float = item.get("quantity", 0.0)
        total += price * quantity
    return total

# 使用示例
items = [
    {"price": 10.0, "quantity": 2},
    {"price": 5.0, "quantity": 4}
]
total = calculate_total(items)
print(f"总金额: {total}")  # 输出: 总金额: 40.0
```

**Expected Output:**
- 函数类型提示正确
- 代码执行正确，输出总金额

### Example 2: Pydantic模型设计

**Scenario:** 设计Pydantic模型用于数据验证和序列化。

**Steps:**
1. 导入必要的Pydantic组件
2. 设计基础模型
3. 设计扩展模型
4. 添加验证器
5. 使用模型进行数据验证

**Code:**
```python
from pydantic import BaseModel, Field, validator
from typing import List, Optional

class ProductBase(BaseModel):
    """产品基础模型"""
    name: str
    price: float = Field(..., gt=0)
    description: Optional[str] = None

class ProductCreate(ProductBase):
    """产品创建模型"""
    category: str
    
    @validator('category')
    def validate_category(cls, v):
        valid_categories = ['electronics', 'clothing', 'books']
        if v not in valid_categories:
            raise ValueError(f'无效的类别，必须是: {valid_categories}')
        return v

class Product(ProductBase):
    """产品响应模型"""
    id: int
    category: str

# 使用示例
try:
    product_data = ProductCreate(
        name="智能手机",
        price=5999.99,
        description="最新款智能手机",
        category="electronics"
    )
    print("产品数据验证成功:", product_data)
    
    # 模拟数据库操作
    product = Product(
        id=1,
        name=product_data.name,
        price=product_data.price,
        description=product_data.description,
        category=product_data.category
    )
    print("产品创建成功:", product)
except Exception as e:
    print("错误:", e)
```

**Expected Output:**
- 产品数据验证成功
- 产品创建成功并输出

### Example 3: mypy配置和使用

**Scenario:** 配置mypy进行类型检查，提高代码类型安全性。

**Steps:**
1. 创建mypy配置文件
2. 运行mypy进行类型检查
3. 修复类型错误

**Code:**
```ini
# mypy.ini
[mypy]
python_version = 3.10
disallow_untyped_defs = True
disallow_incomplete_defs = True
disallow_untyped_calls = True
check_untyped_defs = True
no_implicit_optional = True
```

```python
# example.py
from typing import List, Optional

def greet(name: str) -> str:
    """问候函数"""
    return f"Hello, {name}!"

def process_items(items: List[str]) -> Optional[str]:
    """处理项目"""
    if not items:
        return None
    return items[0]

# 类型错误示例（会被mypy捕获）
def bad_function(x: int) -> str:
    """有类型错误的函数"""
    return x  # 类型错误：返回int而不是str
```

**Expected Output:**
- mypy检测到bad_function的类型错误
- 修复类型错误后mypy检查通过

### Example 4: 类型别名使用

**Scenario:** 使用类型别名简化复杂的类型标注，提高代码可读性。

**Steps:**
1. 定义类型别名
2. 在代码中使用类型别名
3. 验证类型别名的效果

**Code:**
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

# 使用示例
item1 = create_order_item(1, 10.0, 2)
item2 = create_order_item(2, 5.0, 4)
order = create_order(123, [item1, item2])
print("订单:", order)
```

**Expected Output:**
- 类型别名使用正确
- 代码执行正确，输出订单信息

### Example 5: 泛型类型设计

**Scenario:** 设计泛型类型，提高代码的复用性和类型安全性。

**Steps:**
1. 导入必要的泛型组件
2. 定义类型变量
3. 设计泛型类
4. 使用泛型类

**Code:**
```python
from typing import TypeVar, Generic, List, Optional

# 定义类型变量
T = TypeVar('T')

# 设计泛型类
class Queue(Generic[T]):
    """泛型队列类"""
    def __init__(self):
        self.items: List[T] = []
    
    def enqueue(self, item: T) -> None:
        """入队"""
        self.items.insert(0, item)
    
    def dequeue(self) -> Optional[T]:
        """出队"""
        if not self.items:
            return None
        return self.items.pop()
    
    def is_empty(self) -> bool:
        """判断队列是否为空"""
        return len(self.items) == 0
    
    def size(self) -> int:
        """获取队列大小"""
        return len(self.items)

# 使用泛型类
# 字符串队列
string_queue = Queue[str]()
string_queue.enqueue("Hello")
string_queue.enqueue("World")
print(f"字符串队列大小: {string_queue.size()}")
print(f"出队: {string_queue.dequeue()}")

# 整数队列
int_queue = Queue[int]()
int_queue.enqueue(1)
int_queue.enqueue(2)
print(f"整数队列大小: {int_queue.size()}")
print(f"出队: {int_queue.dequeue()}")
```

**Expected Output:**
- 泛型类使用正确
- 代码执行正确，输出队列操作结果

## References

- `references/index.md`: 类型提示最佳实践导航
- `references/typing-module.md`: typing模块使用指南
- `references/pydantic-models.md`: Pydantic模型设计指南
- `references/mypy-configuration.md`: mypy配置指南
- `references/type-aliases.md`: 类型别名使用指南
- `references/generic-types.md`: 泛型类型设计指南

## Maintenance

- Sources: Python官方文档和类型提示最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责运行时类型检查