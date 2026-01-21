# 过滤器专家 (filter-expert)

为C# WebAPI项目提供专业的过滤器开发和配置能力，确保控制器和Action具有一致、高效、安全的执行流程。

## 主要功能

- **ActionFilter实现**：实现和配置Action过滤器
- **异常过滤器**：创建和配置异常处理过滤器
- **模型验证过滤器**：实现和配置模型验证过滤器
- **权限控制过滤器**：创建和配置权限控制过滤器
- **过滤器执行顺序**：管理和控制过滤器的执行顺序

## 使用场景

- 需要实现ActionFilter
- 需要创建异常过滤器
- 需要实现模型验证过滤器
- 需要创建权限控制过滤器
- 需要管理过滤器执行顺序
- 需要优化现有过滤器实现

## 快速开始

### ActionFilter实现
```csharp
// 同步ActionFilter
public class LoggingActionFilter : IActionFilter
{
    private readonly ILogger<LoggingActionFilter> _logger;

    public LoggingActionFilter(ILogger<LoggingActionFilter> logger)
    {
        _logger = logger;
    }

    public void OnActionExecuting(ActionExecutingContext context)
    {
        // 在Action执行之前执行
        var controllerName = context.Controller.GetType().Name;
        var actionName = context.ActionDescriptor.RouteValues["action"];
        _logger.LogInformation("Executing action: {Controller}.{Action}", controllerName, actionName);
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // 在Action执行之后执行
        var controllerName = context.Controller.GetType().Name;
        var actionName = context.ActionDescriptor.RouteValues["action"];
        _logger.LogInformation("Executed action: {Controller}.{Action}", controllerName, actionName);
    }
}

// 注册过滤器
builder.Services.AddControllers(options =>
{
    options.Filters.Add<LoggingActionFilter>();
});
```

### 异常过滤器
```csharp
// 异常过滤器
public class ExceptionFilter : IExceptionFilter
{
    private readonly ILogger<ExceptionFilter> _logger;

    public ExceptionFilter(ILogger<ExceptionFilter> logger)
    {
        _logger = logger;
    }

    public void OnException(ExceptionContext context)
    {
        // 记录异常
        _logger.LogError(context.Exception, "An exception occurred: {Message}", context.Exception.Message);

        // 处理特定类型的异常
        if (context.Exception is NotFoundException)
        {
            context.Result = new NotFoundObjectResult(new
            {
                Message = context.Exception.Message,
                StatusCode = 404
            });
            context.ExceptionHandled = true;
        }
        else
        {
            // 处理其他异常
            context.Result = new ObjectResult(new
            {
                Message = "Internal server error",
                StatusCode = 500
            })
            {
                StatusCode = 500
            };
            context.ExceptionHandled = true;
        }
    }
}
```

### 模型验证过滤器
```csharp
// 自定义模型验证过滤器
public class CustomModelValidationFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        if (!context.ModelState.IsValid)
        {
            // 收集验证错误
            var errors = context.ModelState
                .Where(x => x.Value.Errors.Count > 0)
                .ToDictionary(
                    x => x.Key,
                    x => x.Value.Errors.Select(e => e.ErrorMessage).ToArray()
                );

            // 返回自定义验证错误响应
            context.Result = new BadRequestObjectResult(new
            {
                Message = "Model validation failed",
                Errors = errors,
                StatusCode = 400
            });
        }
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // 不需要实现
    }
}
```

### 权限控制过滤器
```csharp
// 权限控制特性
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
public class RequireRoleAttribute : Attribute, IFilterFactory
{
    private readonly string _role;

    public RequireRoleAttribute(string role)
    {
        _role = role;
    }

    public IFilterMetadata CreateInstance(IServiceProvider serviceProvider)
    {
        return new AuthorizationFilter(_role);
    }

    public bool IsReusable => false;
}

// 使用权限控制过滤器
[ApiController]
[Route("api/[controller]")]
public class AdminController : ControllerBase
{
    [HttpGet]
    [RequireRole("Admin")]
    public ActionResult<string> GetAdminData()
    {
        return "Admin data";
    }
}
```

### 过滤器执行顺序
```csharp
// 过滤器执行顺序
// 1. 授权过滤器 (Authorization filters)
// 2. 资源过滤器 (Resource filters)
// 3. 动作过滤器 (Action filters)
// 4. 异常过滤器 (Exception filters)

// 控制过滤器执行顺序
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
public class CustomFilterAttribute : Attribute, IActionFilter, IOrderedFilter
{
    public int Order { get; set; }

    public void OnActionExecuting(ActionExecutingContext context)
    {
        // 执行逻辑
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // 执行逻辑
    }
}

// 使用Order属性控制顺序
[HttpGet]
[CustomFilter(Order = 1)]
[AnotherFilter(Order = 2)]
public ActionResult<string> Get()
{
    return "Hello";
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件