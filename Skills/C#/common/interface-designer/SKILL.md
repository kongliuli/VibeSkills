---
name: interface-designer
description: "C#接口设计器技能：负责RESTful规范实现、DTO设计模式、分页封装、统一响应格式、API版本管理。使用时需要实现RESTful规范、设计DTO模式、封装分页功能、统一响应格式、管理API版本。"
---

# interface-designer Skill

为C#项目提供专业的接口设计和实现能力，确保项目具有规范、一致、易用的API接口。

## When to Use This Skill

Trigger when any of these applies:
- 需要实现RESTful规范
- 需要设计DTO模式
- 需要封装分页功能
- 需要统一响应格式
- 需要管理API版本
- 需要优化现有接口设计

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行接口设计决策
- 不处理接口的安全性配置（除了基本设计）
- 不负责接口的性能优化（仅设计层面）

## Quick Reference

### Common Patterns

**Pattern 1:** RESTful规范实现
```csharp
// 控制器设计
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    private readonly IUserService _userService;

    public UsersController(IUserService userService)
    {
        _userService = userService;
    }

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

    // PUT: api/users/{id}
    [HttpPut("{id}")]
    public async Task<IActionResult> UpdateUser(int id, UserUpdateDto userUpdate)
    {
        var result = await _userService.UpdateUserAsync(id, userUpdate);
        if (!result)
        {
            return NotFound();
        }
        return NoContent();
    }

    // DELETE: api/users/{id}
    [HttpDelete("{id}")]
    public async Task<IActionResult> DeleteUser(int id)
    {
        var result = await _userService.DeleteUserAsync(id);
        if (!result)
        {
            return NotFound();
        }
        return NoContent();
    }
}
```

**Pattern 2:** DTO设计模式
```csharp
// 实体类
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    public string PasswordHash { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime UpdatedAt { get; set; }
}

// DTO类
public class UserDto
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    public DateTime CreatedAt { get; set; }
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

public class UserUpdateDto
{
    [StringLength(50)]
    public string Name { get; set; }
    
    [EmailAddress]
    public string Email { get; set; }
    
    [MinLength(6)]
    public string Password { get; set; }
}

// 映射扩展
public static class UserMappingExtensions
{
    public static UserDto ToDto(this User user)
    {
        return new UserDto
        {
            Id = user.Id,
            Name = user.Name,
            Email = user.Email,
            CreatedAt = user.CreatedAt
        };
    }

    public static User ToEntity(this UserCreateDto userCreate)
    {
        return new User
        {
            Name = userCreate.Name,
            Email = userCreate.Email,
            PasswordHash = HashPassword(userCreate.Password),
            CreatedAt = DateTime.UtcNow,
            UpdatedAt = DateTime.UtcNow
        };
    }

    public static void UpdateFromDto(this User user, UserUpdateDto userUpdate)
    {
        if (userUpdate.Name != null)
            user.Name = userUpdate.Name;
        if (userUpdate.Email != null)
            user.Email = userUpdate.Email;
        if (userUpdate.Password != null)
            user.PasswordHash = HashPassword(userUpdate.Password);
        user.UpdatedAt = DateTime.UtcNow;
    }

    private static string HashPassword(string password)
    {
        // 密码哈希逻辑
        return BCrypt.Net.BCrypt.HashPassword(password);
    }
}
```

**Pattern 3:** 分页封装
```csharp
// 分页查询参数
public class PaginationQuery
{
    [Range(1, int.MaxValue)]
    public int Page { get; set; } = 1;

    [Range(1, 100)]
    public int PageSize { get; set; } = 10;

    public int Skip => (Page - 1) * PageSize;
}

// 分页响应
public class PagedResponse<T>
{
    public IEnumerable<T> Items { get; set; }
    public int Page { get; set; }
    public int PageSize { get; set; }
    public int TotalItems { get; set; }
    public int TotalPages => (int)Math.Ceiling((double)TotalItems / PageSize);
    public bool HasNextPage => Page < TotalPages;
    public bool HasPreviousPage => Page > 1;

    public PagedResponse(IEnumerable<T> items, int page, int pageSize, int totalItems)
    {
        Items = items;
        Page = page;
        PageSize = pageSize;
        TotalItems = totalItems;
    }
}

// 分页服务
public async Task<PagedResponse<UserDto>> GetUsersAsync(PaginationQuery pagination)
{
    var totalItems = await _userRepository.CountAsync();
    var users = await _userRepository.GetUsersAsync(pagination.Skip, pagination.PageSize);
    var userDtos = users.Select(u => u.ToDto());

    return new PagedResponse<UserDto>(userDtos, pagination.Page, pagination.PageSize, totalItems);
}
```

**Pattern 4:** 统一响应格式
```csharp
// 统一响应模型
public class ApiResponse<T>
{
    public bool Success { get; set; }
    public T Data { get; set; }
    public ErrorResponse Error { get; set; }
    public string TraceId { get; set; }

    private ApiResponse(bool success, T data, ErrorResponse error, string traceId)
    {
        Success = success;
        Data = data;
        Error = error;
        TraceId = traceId;
    }

    public static ApiResponse<T> SuccessResponse(T data, string traceId)
    {
        return new ApiResponse<T>(true, data, null, traceId);
    }

    public static ApiResponse<T> ErrorResponse(string message, string errorCode, int statusCode, string traceId)
    {
        var error = new ErrorResponse
        {
            Message = message,
            ErrorCode = errorCode,
            StatusCode = statusCode
        };
        return new ApiResponse<T>(false, default, error, traceId);
    }
}

public class ErrorResponse
{
    public string Message { get; set; }
    public string ErrorCode { get; set; }
    public int StatusCode { get; set; }
}

// 控制器扩展
public static class ControllerExtensions
{
    public static ActionResult<ApiResponse<T>> ApiOk<T>(this ControllerBase controller, T data)
    {
        var response = ApiResponse<T>.SuccessResponse(data, controller.HttpContext.TraceIdentifier);
        return controller.Ok(response);
    }

    public static ActionResult<ApiResponse<T>> ApiError<T>(this ControllerBase controller, string message, string errorCode, int statusCode)
    {
        var response = ApiResponse<T>.ErrorResponse(message, errorCode, statusCode, controller.HttpContext.TraceIdentifier);
        return controller.StatusCode(statusCode, response);
    }
}

// 使用示例
[HttpGet("{id}")]
public async Task<ActionResult<ApiResponse<UserDto>>> GetUser(int id)
{
    var user = await _userService.GetUserByIdAsync(id);
    if (user == null)
    {
        return this.ApiError<UserDto>("User not found", "USER_NOT_FOUND", StatusCodes.Status404NotFound);
    }
    return this.ApiOk(user);
}
```

**Pattern 5:** API版本管理
```csharp
// 路由版本管理
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
[ApiVersion("2.0")]
public class UsersController : ControllerBase
{
    private readonly IUserService _userService;

    public UsersController(IUserService userService)
    {
        _userService = userService;
    }

    // GET: api/v1/users
    [HttpGet]
    [MapToApiVersion("1.0")]
    public async Task<ActionResult<ApiResponse<IEnumerable<UserDto>>>> GetUsersV1([FromQuery] PaginationQuery pagination)
    {
        var users = await _userService.GetUsersAsync(pagination);
        return this.ApiOk(users);
    }

    // GET: api/v2/users
    [HttpGet]
    [MapToApiVersion("2.0")]
    public async Task<ActionResult<ApiResponse<IEnumerable<UserDtoV2>>>> GetUsersV2([FromQuery] PaginationQuery pagination)
    {
        var users = await _userService.GetUsersAsync(pagination);
        var userDtosV2 = users.Select(u => u.ToDtoV2());
        return this.ApiOk(userDtosV2);
    }
}

// Program.cs中配置
builder.Services.AddApiVersioning(options =>
{
    options.DefaultApiVersion = new ApiVersion(1, 0);
    options.AssumeDefaultVersionWhenUnspecified = true;
    options.ReportApiVersions = true;
});

builder.Services.AddVersionedApiExplorer(options =>
{
    options.GroupNameFormat = "'v'VVV";
    options.SubstituteApiVersionInUrl = true;
});
```

## Examples

### Example 1: 实现RESTful规范
- Input: 需要为用户管理功能实现RESTful API
- Steps:
  1. 创建UsersController控制器
  2. 实现GET、POST、PUT、DELETE方法
  3. 使用适当的HTTP状态码
  4. 遵循RESTful路由命名规范
- Expected output / acceptance: RESTful风格的API接口

### Example 2: 设计DTO模式
- Input: 需要为用户实体设计DTO模式
- Steps:
  1. 创建UserDto（用于返回）
  2. 创建UserCreateDto（用于创建）
  3. 创建UserUpdateDto（用于更新）
  4. 实现实体与DTO之间的映射
- Expected output / acceptance: 清晰的DTO设计和映射

### Example 3: 封装分页功能
- Input: 需要为列表接口实现分页功能
- Steps:
  1. 创建PaginationQuery类接收分页参数
  2. 创建PagedResponse类返回分页结果
  3. 在服务层实现分页查询逻辑
  4. 在控制器中使用分页参数
- Expected output / acceptance: 完整的分页功能

## References

- `references/index.md`: 接口设计最佳实践导航
- `references/restful-patterns.md`: RESTful规范实现指南
- `references/dto-design.md`: DTO设计模式指南
- `references/pagination.md`: 分页功能封装指南
- `references/response-format.md`: 统一响应格式指南
- `references/api-versioning.md`: API版本管理指南

## Maintenance

- Sources: RESTful API设计最佳实践和ASP.NET Core官方文档
- Last updated: 2026-01-21
- Known limits: 不负责接口的安全性配置和性能优化