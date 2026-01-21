# Python FastAPI生成器

## 技能描述

**Python FastAPI生成器**是一个专为Python项目设计的专业技能，专注于提供高质量的FastAPI应用程序生成解决方案。该技能涵盖了路由定义、依赖注入、Pydantic模型、异步操作和API文档自动生成等核心领域，帮助开发人员快速构建高性能、类型安全的API应用程序。

### 专注领域
- **路由定义**：创建和管理API路由，支持路径参数、查询参数和请求体
- **依赖注入**：实现依赖注入系统，支持共享逻辑和认证
- **Pydantic模型**：设计和使用Pydantic模型进行数据验证和序列化
- **异步操作**：实现异步API端点，提高应用性能
- **API文档**：自动生成交互式API文档，提高开发和使用体验

## 目录结构

```plaintext
skills/Python/fastapi-generator/
├── SKILL.md          # 技能技术规范文件
├── README.md         # 中文文档
├── references/       # 参考资料
├── scripts/          # 相关脚本
└── assets/           # 资源文件
```

## 快速开始

### 基本使用

1. **安装FastAPI**
   - 安装FastAPI和uvicorn
   - 安装必要的依赖

2. **创建基本应用**
   - 导入FastAPI
   - 创建应用实例
   - 定义路由

3. **运行应用**
   - 使用uvicorn运行应用
   - 访问API文档

4. **扩展应用**
   - 添加Pydantic模型
   - 实现依赖注入
   - 添加异步操作

## 使用示例

### 示例1：基本FastAPI应用

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    """根路径"""
    return {"Hello": "World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    """获取项目"""
    return {"item_id": item_id, "q": q}

# 运行应用
# uvicorn main:app --reload
```

### 示例2：依赖注入和Pydantic模型

```python
from fastapi import FastAPI, Depends
from pydantic import BaseModel
from typing import List, Optional

app = FastAPI()

# Pydantic模型
class Item(BaseModel):
    """项目模型"""
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

class ItemResponse(Item):
    """项目响应模型"""
    id: int

# 模拟数据库
items = [
    {"id": 1, "name": "Item 1", "price": 10.0},
    {"id": 2, "name": "Item 2", "price": 20.0},
]

# 依赖函数
def get_db():
    """获取数据库"""
    return items

# 路由
@app.get("/items", response_model=List[ItemResponse])
def get_items(db: List[dict] = Depends(get_db)):
    """获取项目列表"""
    return db

@app.post("/items", response_model=ItemResponse)
def create_item(item: Item, db: List[dict] = Depends(get_db)):
    """创建项目"""
    new_item = item.dict()
    new_item["id"] = len(db) + 1
    db.append(new_item)
    return new_item
```

### 示例3：异步操作

```python
from fastapi import FastAPI
import asyncio
from typing import List

app = FastAPI()

# 模拟异步数据库操作
async def get_items_from_db():
    """从数据库获取项目"""
    await asyncio.sleep(0.5)  # 模拟数据库延迟
    return [
        {"id": 1, "name": "Item 1", "price": 10.0},
        {"id": 2, "name": "Item 2", "price": 20.0},
    ]

async def get_item_from_db(item_id: int):
    """从数据库获取单个项目"""
    await asyncio.sleep(0.3)  # 模拟数据库延迟
    items = await get_items_from_db()
    for item in items:
        if item["id"] == item_id:
            return item
    return None

# 异步路由
@app.get("/items", response_model=List[dict])
async def get_items():
    """获取项目列表"""
    items = await get_items_from_db()
    return items

@app.get("/items/{item_id}", response_model=dict)
async def get_item(item_id: int):
    """获取项目"""
    item = await get_item_from_db(item_id)
    if item:
        return item
    return {"error": "Item not found"}

# 后台任务
@app.post("/items")
async def create_item(item: dict):
    """创建项目"""
    # 执行后台任务
    async def background_task():
        await asyncio.sleep(2)  # 模拟长时间操作
        print(f"Background task completed for item: {item['name']}")
    
    asyncio.create_task(background_task())
    return {"message": "Item created", "item": item}
```

### 示例4：完整的API应用

```python
from fastapi import FastAPI, Depends, HTTPException, status
from pydantic import BaseModel, EmailStr
from typing import List, Optional
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm

# 创建FastAPI应用
app = FastAPI(
    title="User and Item API",
    description="A complete FastAPI application for user and item management",
    version="1.0.0",
)

# 安全配置
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Pydantic模型
class UserBase(BaseModel):
    """用户基础模型"""
    email: EmailStr
    name: str

class UserCreate(UserBase):
    """用户创建模型"""
    password: str

class User(UserBase):
    """用户响应模型"""
    id: int
    is_active: bool

    class Config:
        orm_mode = True

class ItemBase(BaseModel):
    """项目基础模型"""
    title: str
    description: Optional[str] = None

class ItemCreate(ItemBase):
    """项目创建模型"""
    pass

class Item(ItemBase):
    """项目响应模型"""
    id: int
    owner_id: int

    class Config:
        orm_mode = True

# 模拟数据库
users_db = [
    {"id": 1, "email": "user1@example.com", "name": "User 1", "password": "password", "is_active": True},
    {"id": 2, "email": "user2@example.com", "name": "User 2", "password": "password", "is_active": True},
]

items_db = [
    {"id": 1, "title": "Item 1", "description": "First item", "owner_id": 1},
    {"id": 2, "title": "Item 2", "description": "Second item", "owner_id": 1},
    {"id": 3, "title": "Item 3", "description": "Third item", "owner_id": 2},
]

# 依赖注入
def get_current_user(token: str = Depends(oauth2_scheme)):
    """获取当前用户"""
    user = next((u for u in users_db if u["email"] == token), None)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Could not validate credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )
    return user

# 认证路由
@app.post("/token")
def login(form_data: OAuth2PasswordRequestForm = Depends()):
    """用户登录"""
    user = next((u for u in users_db if u["email"] == form_data.username), None)
    if not user or user["password"] != form_data.password:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )
    return {"access_token": user["email"], "token_type": "bearer"}

# 用户路由
@app.get("/users", response_model=List[User], tags=["users"])
def get_users(current_user: dict = Depends(get_current_user)):
    """获取用户列表"""
    return users_db

@app.post("/users", response_model=User, tags=["users"])
def create_user(user: UserCreate):
    """创建用户"""
    new_user = {
        "id": len(users_db) + 1,
        "email": user.email,
        "name": user.name,
        "password": user.password,
        "is_active": True,
    }
    users_db.append(new_user)
    return new_user

# 项目路由
@app.get("/items", response_model=List[Item], tags=["items"])
def get_items(current_user: dict = Depends(get_current_user)):
    """获取项目列表"""
    return items_db

@app.post("/items", response_model=Item, tags=["items"])
def create_item(item: ItemCreate, current_user: dict = Depends(get_current_user)):
    """创建项目"""
    new_item = {
        "id": len(items_db) + 1,
        "title": item.title,
        "description": item.description,
        "owner_id": current_user["id"],
    }
    items_db.append(new_item)
    return new_item

@app.get("/items/{item_id}", response_model=Item, tags=["items"])
def get_item(item_id: int, current_user: dict = Depends(get_current_user)):
    """获取项目"""
    item = next((i for i in items_db if i["id"] == item_id), None)
    if not item:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Item not found",
        )
    return item
```

### 示例5：路由分组和中间件

```python
from fastapi import FastAPI, APIRouter, Depends, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import List

# 创建FastAPI应用
app = FastAPI(
    title="API with Router Groups and Middleware",
    description="A FastAPI application demonstrating router groups and middleware",
    version="1.0.0",
)

# 配置CORS中间件
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # 在生产环境中应该设置具体的域名
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# 自定义中间件
@app.middleware("http")
async def add_process_time_header(request, call_next):
    """添加处理时间头"""
    import time
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    response.headers["X-Process-Time"] = str(process_time)
    return response

# Pydantic模型
class User(BaseModel):
    """用户模型"""
    id: int
    name: str
    email: str

class Item(BaseModel):
    """项目模型"""
    id: int
    name: str
    price: float

# 创建路由分组
user_router = APIRouter(prefix="/users", tags=["users"])
item_router = APIRouter(prefix="/items", tags=["items"])

# 模拟数据
users = [
    {"id": 1, "name": "User 1", "email": "user1@example.com"},
    {"id": 2, "name": "User 2", "email": "user2@example.com"},
]

items = [
    {"id": 1, "name": "Item 1", "price": 10.0},
    {"id": 2, "name": "Item 2", "price": 20.0},
]

# 用户路由
@user_router.get("", response_model=List[User])
def get_users():
    """获取用户列表"""
    return users

@user_router.get("/{user_id}", response_model=User)
def get_user(user_id: int):
    """获取用户"""
    user = next((u for u in users if u["id"] == user_id), None)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user

# 项目路由
@item_router.get("", response_model=List[Item])
def get_items():
    """获取项目列表"""
    return items

@item_router.get("/{item_id}", response_model=Item)
def get_item(item_id: int):
    """获取项目"""
    item = next((i for i in items if i["id"] == item_id), None)
    if not item:
        raise HTTPException(status_code=404, detail="Item not found")
    return item

# 注册路由分组
app.include_router(user_router)
app.include_router(item_router)

# 根路径
@app.get("/")
def read_root():
    """根路径"""
    return {"message": "Welcome to the API"}

# 健康检查
@app.get("/health")
def health_check():
    """健康检查"""
    return {"status": "healthy"}
```

## 最佳实践

1. **项目结构**
   - 使用模块化的项目结构
   - 分离路由、模型、依赖和服务
   - 使用路由分组组织API端点

2. **路由设计**
   - 使用RESTful API设计原则
   - 为路由添加清晰的文档字符串
   - 使用合适的HTTP方法
   - 为路由添加适当的标签

3. **Pydantic模型**
   - 为请求和响应使用不同的模型
   - 使用模型继承减少代码重复
   - 添加适当的字段验证
   - 为模型添加清晰的文档

4. **依赖注入**
   - 使用依赖注入共享逻辑
   - 为依赖添加类型提示
   - 合理使用依赖层级
   - 为认证和授权使用依赖

5. **异步操作**
   - 对I/O操作使用异步函数
   - 使用后台任务处理长时间运行的操作
   - 避免在异步函数中使用阻塞操作
   - 合理使用asyncio.create_task

6. **API文档**
   - 为所有路由添加清晰的文档字符串
   - 配置API的标题、描述和版本
   - 使用标签组织API端点
   - 为请求和响应添加适当的模型

7. **安全性**
   - 使用HTTPS
   - 实现适当的认证和授权
   - 验证所有用户输入
   - 处理错误和异常

## 常见问题

### 1. 应用启动失败

**问题**：使用uvicorn运行应用时失败，提示模块未找到。

**解决方案**：
- 确保已安装FastAPI和uvicorn
- 确保运行命令的路径正确
- 检查模块名称是否正确

### 2. 路由冲突

**问题**：添加新路由时出现路由冲突错误。

**解决方案**：
- 确保路由路径唯一
- 检查路径参数的顺序
- 避免使用重叠的路由模式

### 3. Pydantic验证错误

**问题**：请求数据时出现Pydantic验证错误。

**解决方案**：
- 确保请求数据符合Pydantic模型定义
- 检查字段类型和约束
- 查看详细的错误信息

### 4. 依赖注入错误

**问题**：依赖注入失败，提示依赖项未找到。

**解决方案**：
- 确保依赖函数正确定义
- 检查依赖参数的类型
- 确保依赖函数返回正确的类型

### 5. 异步操作阻塞

**问题**：异步操作运行缓慢，似乎被阻塞。

**解决方案**：
- 确保在异步函数中使用await
- 避免在异步函数中使用阻塞操作
- 对I/O操作使用异步版本

## 资源链接

- [FastAPI官方文档](https://fastapi.tiangolo.com/zh/)
- [Pydantic官方文档](https://docs.pydantic.dev/)
- [Uvicorn官方文档](https://www.uvicorn.org/)
- [FastAPI教程](https://fastapi.tiangolo.com/zh/tutorial/)
- [FastAPI最佳实践](https://fastapi.tiangolo.com/zh/best-practices/)

## 总结

**Python FastAPI生成器**技能为Python项目提供了全面的FastAPI应用程序生成解决方案，涵盖了从基本路由定义到复杂的依赖注入、Pydantic模型设计、异步操作和API文档生成的各个方面。通过遵循本技能提供的最佳实践和示例代码，开发人员可以：

- 快速构建高性能、类型安全的API应用程序
- 自动生成交互式API文档
- 实现高效的异步操作
- 构建模块化、可维护的代码结构
- 确保API的安全性和可靠性

无论是构建新的API应用还是维护现有项目，**Python FastAPI生成器**技能都能为开发人员提供有力的支持，帮助他们构建高质量的FastAPI应用程序。