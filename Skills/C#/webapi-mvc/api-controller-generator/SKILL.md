---
name: api-controller-generator
description: "C# API控制器生成器技能：负责Controller类生成、路由属性配置([Route/[HttpGet])、参数绑定策略、文件上传处理、Action返回值规范。使用时需要生成API控制器、配置路由属性、设置参数绑定策略、处理文件上传、规范Action返回值。"
---

# api-controller-generator Skill

为C# WebAPI项目提供专业的API控制器生成和配置能力，确保控制器具有规范、一致、高效的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要生成API控制器类
- 需要配置路由属性([Route/[HttpGet]等)
- 需要设置参数绑定策略
- 需要处理文件上传
- 需要规范Action返回值
- 需要优化现有控制器实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行控制器设计决策
- 不处理控制器的安全性配置（除了基本设计）
- 不负责控制器的性能优化（仅设计层面）

## Quick Reference

### Common Patterns

**Pattern 1:** 基本控制器结构
```csharp
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
    public async Task<ActionResult<IEnumerable<UserDto>>> GetUsers()
    {
        var users = await _userService.GetUsersAsync();
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

**Pattern 2:** 路由属性配置
```csharp
// 基本路由
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    // GET: api/products
    [HttpGet]
    public ActionResult<IEnumerable<ProductDto>> GetProducts() { ... }

    // GET: api/products/{id}
    [HttpGet("{id}")]
    public ActionResult<ProductDto> GetProduct(int id) { ... }
}

// 自定义路由
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
public class OrdersController : ControllerBase
{
    // GET: api/v1/orders
    [HttpGet]
    public ActionResult<IEnumerable<OrderDto>> GetOrders() { ... }

    // GET: api/v1/orders/{id}/items
    [HttpGet("{id}/items")]
    public ActionResult<IEnumerable<OrderItemDto>> GetOrderItems(int id) { ... }
}

// 动词约束路由
[ApiController]
[Route("api/[controller]")]
public class CustomersController : ControllerBase
{
    // GET: api/customers/search?name=John
    [HttpGet("search")]
    public ActionResult<IEnumerable<CustomerDto>> SearchCustomers([FromQuery] string name) { ... }
}
```

**Pattern 3:** 参数绑定策略
```csharp
[ApiController]
[Route("api/[controller]")]
public class ValuesController : ControllerBase
{
    // 路由参数绑定
    [HttpGet("{id}")]
    public ActionResult<string> GetById(int id) { ... }

    // 查询参数绑定
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get([FromQuery] string filter, [FromQuery] int page = 1) { ... }

    // 请求体绑定
    [HttpPost]
    public ActionResult<string> Post([FromBody] string value) { ... }

    // 表单数据绑定
    [HttpPost("form")]
    public ActionResult<string> PostForm([FromForm] string name, [FromForm] int age) { ... }

    // 标头绑定
    [HttpGet("header")]
    public ActionResult<string> GetFromHeader([FromHeader(Name = "X-User-ID")] int userId) { ... }

    // 依赖注入绑定
    [HttpGet("di")]
    public ActionResult<string> GetWithDependency([FromServices] IMyService service) { ... }
}
```

**Pattern 4:** 文件上传处理
```csharp
[ApiController]
[Route("api/[controller]")]
public class UploadController : ControllerBase
{
    // 单文件上传
    [HttpPost("single")]
    public async Task<ActionResult<string>> UploadSingle(IFormFile file)
    {
        if (file == null || file.Length == 0)
        {
            return BadRequest("No file uploaded.");
        }

        var filePath = Path.Combine(Directory.GetCurrentDirectory(), "uploads", file.FileName);
        
        using (var stream = new FileStream(filePath, FileMode.Create))
        {
            await file.CopyToAsync(stream);
        }

        return Ok($"File uploaded successfully: {file.FileName}");
    }

    // 多文件上传
    [HttpPost("multiple")]
    public async Task<ActionResult<IEnumerable<string>>> UploadMultiple(IFormFileCollection files)
    {
        if (files == null || files.Count == 0)
        {
            return BadRequest("No files uploaded.");
        }

        var uploadedFiles = new List<string>();
        var uploadsDir = Path.Combine(Directory.GetCurrentDirectory(), "uploads");
        
        if (!Directory.Exists(uploadsDir))
        {
            Directory.CreateDirectory(uploadsDir);
        }

        foreach (var file in files)
        {
            if (file.Length > 0)
            {
                var filePath = Path.Combine(uploadsDir, file.FileName);
                
                using (var stream = new FileStream(filePath, FileMode.Create))
                {
                    await file.CopyToAsync(stream);
                }

                uploadedFiles.Add(file.FileName);
            }
        }

        return Ok(uploadedFiles);
    }

    // 带额外数据的文件上传
    [HttpPost("with-data")]
    public async Task<ActionResult<string>> UploadWithData([FromForm] UploadModel model)
    {
        if (model.File == null || model.File.Length == 0)
        {
            return BadRequest("No file uploaded.");
        }

        // 处理文件和额外数据
        var fileName = $"{model.Name}_{model.File.FileName}";
        var filePath = Path.Combine(Directory.GetCurrentDirectory(), "uploads", fileName);
        
        using (var stream = new FileStream(filePath, FileMode.Create))
        {
            await model.File.CopyToAsync(stream);
        }

        return Ok($"File uploaded successfully for {model.Name}: {fileName}");
    }
}

public class UploadModel
{
    public string Name { get; set; }
    public IFormFile File { get; set; }
}
```

**Pattern 5:** Action返回值规范
```csharp
[ApiController]
[Route("api/[controller]")]
public class ReturnsController : ControllerBase
{
    // 返回200 OK with data
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        var values = new string[] { "value1", "value2" };
        return Ok(values);
    }

    // 返回201 Created
    [HttpPost]
    public ActionResult<string> Post([FromBody] string value)
    {
        // 处理创建逻辑
        return CreatedAtAction(nameof(Get), new { id = 1 }, value);
    }

    // 返回204 No Content
    [HttpPut("{id}")]
    public IActionResult Put(int id, [FromBody] string value)
    {
        // 处理更新逻辑
        return NoContent();
    }

    // 返回400 Bad Request
    [HttpGet("badrequest")]
    public ActionResult<string> GetBadRequest()
    {
        return BadRequest("Invalid request");
    }

    // 返回404 Not Found
    [HttpGet("{id}")]
    public ActionResult<string> GetNotFound(int id)
    {
        return NotFound();
    }

    // 返回401 Unauthorized
    [HttpGet("unauthorized")]
    public ActionResult<string> GetUnauthorized()
    {
        return Unauthorized();
    }

    // 返回403 Forbidden
    [HttpGet("forbidden")]
    public ActionResult<string> GetForbidden()
    {
        return Forbid();
    }

    // 返回500 Internal Server Error
    [HttpGet("error")]
    public ActionResult<string> GetError()
    {
        try
        {
            throw new Exception("Test exception");
        }
        catch (Exception ex)
        {
            return StatusCode(500, "Internal server error");
        }
    }
}
```

## Examples

### Example 1: 生成基本API控制器
- Input: 需要为用户管理功能生成API控制器
- Steps:
  1. 创建UsersController类继承ControllerBase
  2. 添加[ApiController]和[Route]属性
  3. 实现构造函数注入依赖
  4. 添加CRUD操作的Action方法
  5. 配置相应的HTTP动词属性
- Expected output / acceptance: 完整的API控制器实现

### Example 2: 配置参数绑定策略
- Input: 需要为搜索接口配置参数绑定
- Steps:
  1. 分析需要的参数类型（路由参数、查询参数、请求体等）
  2. 为每个参数添加适当的绑定属性（[FromRoute]、[FromQuery]、[FromBody]等）
  3. 设置参数验证属性
  4. 测试参数绑定是否正确
- Expected output / acceptance: 参数绑定策略正确配置

### Example 3: 实现文件上传处理
- Input: 需要为系统添加文件上传功能
- Steps:
  1. 创建UploadController类
  2. 添加文件上传的Action方法
  3. 配置适当的HTTP动词（通常是POST）
  4. 实现文件保存逻辑
  5. 处理错误情况
- Expected output / acceptance: 文件上传功能正常工作

## References

- `references/index.md`: API控制器最佳实践导航
- `references/controller-generation.md`: 控制器生成指南
- `references/routing-configuration.md`: 路由属性配置指南
- `references/parameter-binding.md`: 参数绑定策略指南
- `references/file-upload.md`: 文件上传处理指南
- `references/action-returns.md`: Action返回值规范指南

## Maintenance

- Sources: ASP.NET Core官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现