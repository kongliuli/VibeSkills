# 接口设计器 (interface-designer)

为C#项目提供专业的接口设计和实现能力，确保项目具有规范、一致、易用的API接口。

## 主要功能

- **RESTful规范实现**：实现符合RESTful规范的API接口
- **DTO设计模式**：设计和实现数据传输对象(DTO)模式
- **分页封装**：封装和实现分页功能
- **统一响应格式**：设计和使用统一的响应格式
- **API版本管理**：管理和实现API版本控制

## 使用场景

- 需要实现RESTful规范
- 需要设计DTO模式
- 需要封装分页功能
- 需要统一响应格式
- 需要管理API版本
- 需要优化现有接口设计

## 快速开始

### RESTful规范实现
```csharp
// 控制器设计
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    // GET: api/users
    [HttpGet]
    public async Task<ActionResult<IEnumerable<UserDto>>> GetUsers([FromQuery] PaginationQuery pagination)
    {
        var users = await _userService.GetUsersAsync(pagination);
        return Ok(users);
    }

    // GET: api/users/{id}
    [HttpGet("{id}")]
    public async Task<ActionResult<UserDto>> GetUser(int id)
    {
        var user = await _userService.GetUserByIdAsync(id);
        if (user == null)
        {
            return NotFound();
        }
        return Ok(user);
    }

    // POST: api/users
    [HttpPost]
    public async Task<ActionResult<UserDto>> CreateUser(UserCreateDto userCreate)
    {
        var user = await _userService.CreateUserAsync(userCreate);
        return CreatedAtAction(nameof(GetUser), new { id = user.Id }, user);
    }
}
```

### DTO设计模式
```csharp
// 实体类
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    public string PasswordHash { get; set; }
}

// DTO类
public class UserDto
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
}

public class UserCreateDto
{
    [Required]
    [StringLength(50)]
    public string Name { get; set; }
    
    [Required]
    [EmailAddress]
    public string Email { get; set; }
    
    [Required]
    [MinLength(6)]
    public string Password { get; set; }
}
```

### 分页封装
```csharp
// 分页查询参数
public class PaginationQuery
{
    [Range(1, int.MaxValue)]
    public int Page { get; set; } = 1;

    [Range(1, 100)]
    public int PageSize { get; set; } = 10;
}

// 分页响应
public class PagedResponse<T>
{
    public IEnumerable<T> Items { get; set; }
    public int Page { get; set; }
    public int PageSize { get; set; }
    public int TotalItems { get; set; }
    public int TotalPages { get; set; }
    public bool HasNextPage { get; set; }
    public bool HasPreviousPage { get; set; }
}
```

### 统一响应格式
```csharp
// 统一响应模型
public class ApiResponse<T>
{
    public bool Success { get; set; }
    public T Data { get; set; }
    public ErrorResponse Error { get; set; }
    public string TraceId { get; set; }
}

// 使用示例
[HttpGet("{id}")]
public async Task<ActionResult<ApiResponse<UserDto>>> GetUser(int id)
{
    var user = await _userService.GetUserByIdAsync(id);
    if (user == null)
    {
        return this.ApiError<UserDto>("User not found", "USER_NOT_FOUND", 404);
    }
    return this.ApiOk(user);
}
```

### API版本管理
```csharp
// 路由版本管理
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
[ApiVersion("2.0")]
public class UsersController : ControllerBase
{
    [HttpGet]
    [MapToApiVersion("1.0")]
    public async Task<ActionResult<ApiResponse<IEnumerable<UserDto>>>> GetUsersV1([FromQuery] PaginationQuery pagination)
    {
        // 实现
    }

    [HttpGet]
    [MapToApiVersion("2.0")]
    public async Task<ActionResult<ApiResponse<IEnumerable<UserDtoV2>>>> GetUsersV2([FromQuery] PaginationQuery pagination)
    {
        // 实现
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件