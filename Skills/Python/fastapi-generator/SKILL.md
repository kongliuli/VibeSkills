---
name: python-fastapi-generator
description: "Python FastAPI生成器技能：负责路由定义、依赖注入、Pydantic模型、异步操作、API文档自动生成。使用时需要定义路由、配置依赖注入、设计Pydantic模型、实现异步操作、生成API文档。"
---

# python-fastapi-generator Skill

为Python项目提供专业的FastAPI应用程序生成能力，确保应用能够正确实现路由定义、依赖注入、Pydantic模型、异步操作和API文档自动生成，提高开发效率和API质量。

## When to Use This Skill

Trigger when any of these applies:
- 需要定义FastAPI路由
- 需要配置依赖注入
- 需要设计Pydantic模型
- 需要实现异步操作
- 需要生成API文档
- 需要优化现有FastAPI应用

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行API设计决策
- 不处理深度性能优化（仅实现层面）
- 不负责数据库操作的具体实现

## Quick Reference

### Common Patterns

**Pattern 1:** 路由定义
```python
# 基本路由定义
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

# 路由路径参数
@app.get("/users/{user_id}/items/{item_id}")
def read_user_item(user_id: int, item_id: int, q: str = None, short: bool = False):
    """获取用户项目"""
    item = {"item_id": item_id, "owner_id": user_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update({"description": "This is an amazing item that has a long description"})
    return item

# 路由查询参数
@app.get("/items/")
def read_items(skip: int = 0, limit: int = 10):
    """获取项目列表"""
    return {"skip": skip, "limit": limit}

# 路由分组
from fastapi import APIRouter

router = APIRouter(prefix="/api", tags=["items"])

@router.get("/items/")
def get_items():
    """获取API项目列表"""
    return [{"item_id": 1, "name": "Item 1"}]

@router.post("/items/")
def create_item(item: dict):
    """创建API项目"""
    return {"item": item}

# 将路由添加到应用
app.include_router(router)
```

**Pattern 2:** 依赖注入
```python
# 基本依赖注入
from fastapi import FastAPI, Depends

app = FastAPI()

# 依赖函数
def get_db():
    """获取数据库连接"""
    db = "Database connection"
    try:
        yield db
    finally:
        print("Closing database connection")

# 使用依赖
@app.get("/items/")
def read_items(db: str = Depends(get_db)):
    """获取项目列表"""
    return {"db": db, "items": []}

# 类依赖
from typing import Optional

class QueryParams:
    """查询参数依赖"""
    def __init__(self, q: Optional[str] = None, skip: int = 0, limit: int = 100):
        self.q = q
        self.skip = skip
        self.limit = limit

@app.get("/items/")
def read_items(params: QueryParams = Depends(QueryParams)):
    """获取项目列表"""
    return {"q": params.q, "skip": params.skip, "limit": params.limit}

# 路径操作装饰器依赖
from fastapi import Depends, HTTPException, status

async def verify_token(x_token: str = Depends()):
    """验证令牌"""
    if x_token != "fake-super-secret-token":
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Could not validate credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )

@app.get("/items", dependencies=[Depends(verify_token)])
async def read_items():
    """获取项目列表（需要验证）"""
    return [{"item": "Foo"}, {"item": "Bar"}]

# 全局依赖
app = FastAPI(dependencies=[Depends(verify_token)])
```

**Pattern 3:** Pydantic模型
```python
# 基本Pydantic模型
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    """项目模型"""
    name: str
    description: str = None
    price: float
    tax: float = None

@app.post("/items/")
def create_item(item: Item):
    """创建项目"""
    return item

# Pydantic模型继承
class UserBase(BaseModel):
    """用户基础模型"""
    email: str
    full_name: str = None

class UserCreate(UserBase):
    """用户创建模型"""
    password: str

class User(UserBase):
    """用户响应模型"""
    id: int
    active: bool = True

    class Config:
        orm_mode = True

# Pydantic模型验证
from pydantic import BaseModel, Field, EmailStr, validator

class User(BaseModel):
    """用户模型"""
    id: int
    name: str = Field(..., min_length=2, max_length=50)
    email: EmailStr
    password: str = Field(..., min_length=8)
    age: int = Field(..., ge=0, le=150)
    
    @validator('password')
    def password_strength(cls, v):
        """密码强度验证"""
        if not any(char.isdigit() for char in v):
            raise ValueError('密码必须包含至少一个数字')
        if not any(char.isupper() for char in v):
            raise ValueError('密码必须包含至少一个大写字母')
        return v
```

**Pattern 4:** 异步操作
```python
# 基本异步操作
from fastapi import FastAPI
import asyncio

app = FastAPI()

@app.get("/")
async def read_root():
    """根路径"""
    return {"Hello": "World"}

# 异步函数
async def background_task():
    """后台任务"""
    await asyncio.sleep(1)
    print("Background task completed")

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    """获取项目"""
    # 执行后台任务
    asyncio.create_task(background_task())
    return {"item_id": item_id}

# 异步依赖
async def get_db():
    """获取数据库连接"""
    db = "Database connection"
    try:
        yield db
    finally:
        print("Closing database connection")

@app.get("/items/")
async def read_items(db: str = Depends(get_db)):
    """获取项目列表"""
    await asyncio.sleep(0.5)  # 模拟异步操作
    return {"db": db, "items": []}

# 异步数据库操作
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
from sqlalchemy.orm import sessionmaker

# 创建异步引擎
engine = create_async_engine("sqlite+aiosqlite:///./test.db")
AsyncSessionLocal = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)

# 异步数据库依赖
async def get_db():
    """获取数据库会话"""
    async with AsyncSessionLocal() as session:
        try:
            yield session
        finally:
            await session.close()

@app.get("/users/")
async def get_users(db: AsyncSession = Depends(get_db)):
    """获取用户列表"""
    # 执行异步数据库查询
    result = await db.execute("SELECT * FROM users")
    users = result.fetchall()
    return users
```

**Pattern 5:** API文档自动生成
```python
# 基本API文档配置
from fastapi import FastAPI

app = FastAPI(
    title="Sample API",
    description="A sample FastAPI application",
    version="0.1.0",
)

@app.get("/")
def read_root():
    """根路径"""
    return {"Hello": "World"}

# 路径操作文档
@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    """
    获取项目
    
    - **item_id**: 项目ID
    - **q**: 查询参数
    """
    return {"item_id": item_id, "q": q}

# 标签和摘要
@app.get("/users", tags=["users"], summary="获取用户列表")
def get_users():
    """获取所有用户的列表"""
    return [{"user_id": 1, "name": "User 1"}]

# 响应模型文档
from pydantic import BaseModel

class Item(BaseModel):
    """项目模型"""
    name: str
    description: str = None
    price: float
    tax: float = None

@app.post("/items", tags=["items"], response_model=Item)
def create_item(item: Item):
    """创建新项目"""
    return item

# 安全文档
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.post("/token")
def login(form_data: OAuth2PasswordRequestForm = Depends()):
    """获取访问令牌"""
    return {"access_token": form_data.username, "token_type": "bearer"}

@app.get("/protected")
def protected_route(token: str = Depends(oauth2_scheme)):
    """受保护的路由"""
    return {"token": token}
```

## Examples

### Example 1: 基本FastAPI应用

**Scenario:** 创建一个基本的FastAPI应用，包含根路径和项目路径。

**Steps:**
1. 导入FastAPI
2. 创建FastAPI应用实例
3. 定义根路径路由
4. 定义项目路径路由
5. 运行应用

**Code:**
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

**Expected Output:**
- 应用启动成功
- 访问根路径返回 `{"Hello": "World"}`
- 访问 `/items/1` 返回 `{"item_id": 1, "q": null}`
- API文档可通过 `/docs` 访问

### Example 2: 依赖注入和Pydantic模型

**Scenario:** 创建一个使用依赖注入和Pydantic模型的FastAPI应用。

**Steps:**
1. 导入必要的模块
2. 创建FastAPI应用实例
3. 定义Pydantic模型
4. 定义依赖函数
5. 定义路由
6. 运行应用

**Code:**
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

@app.get("/items/{item_id}", response_model=ItemResponse)
def get_item(item_id: int, db: List[dict] = Depends(get_db)):
    """获取项目"""
    for item in db:
        if item["id"] == item_id:
            return item
    return {"id": item_id, "name": "Not found", "price": 0.0}
```

**Expected Output:**
- 应用启动成功
- 访问 `/items` 返回项目列表
- POST 到 `/items` 创建新项目
- 访问 `/items/1` 返回指定项目
- API文档显示请求和响应模型

### Example 3: 异步操作

**Scenario:** 创建一个使用异步操作的FastAPI应用。

**Steps:**
1. 导入必要的模块
2. 创建FastAPI应用实例
3. 定义异步路由
4. 实现异步操作
5. 运行应用

**Code:**
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

**Expected Output:**
- 应用启动成功
- 访问 `/items` 返回项目列表（有轻微延迟）
- 访问 `/items/1` 返回指定项目（有轻微延迟）
- POST 到 `/items` 立即返回，后台任务继续执行

### Example 4: 完整的API应用

**Scenario:** 创建一个完整的API应用，包含用户和项目管理。

**Steps:**
1. 导入必要的模块
2. 创建FastAPI应用实例
3. 定义Pydantic模型
4. 定义依赖注入
5. 定义路由
6. 配置API文档
7. 运行应用

**Code:**
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

**Expected Output:**
- 应用启动成功
- API文档可通过 `/docs` 访问
- 支持用户认证
- 支持用户和项目的CRUD操作
- 所有操作都有详细的API文档

### Example 5: 路由分组和中间件

**Scenario:** 创建一个使用路由分组和中间件的FastAPI应用。

**Steps:**
1. 导入必要的模块
2. 创建FastAPI应用实例
3. 定义中间件
4. 定义路由分组
5. 注册路由分组
6. 运行应用

**Code:**
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

**Expected Output:**
- 应用启动成功
- 中间件正常工作（添加X-Process-Time头）
- CORS配置生效
- 路由分组正常工作（/users和/items路径）
- 健康检查端点可访问

## References

- `references/index.md`: FastAPI开发最佳实践导航
- `references/routing.md`: 路由定义指南
- `references/dependency-injection.md`: 依赖注入指南
- `references/pydantic-models.md`: Pydantic模型设计指南
- `references/async-operations.md`: 异步操作指南
- `references/api-documentation.md`: API文档生成指南

## Maintenance

- Sources: FastAPI官方文档和最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责数据库操作的具体实现