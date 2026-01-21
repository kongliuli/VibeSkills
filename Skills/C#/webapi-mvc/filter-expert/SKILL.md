---
name: filter-expert
description: "C#过滤器专家技能：负责ActionFilter实现、异常过滤器、模型验证过滤器、权限控制过滤器、过滤器执行顺序。使用时需要实现ActionFilter、创建异常过滤器、实现模型验证过滤器、创建权限控制过滤器、管理过滤器执行顺序。"
---

# filter-expert Skill

为C# WebAPI项目提供专业的过滤器开发和配置能力，确保控制器和Action具有一致、高效、安全的执行流程。

## When to Use This Skill

Trigger when any of these applies:
- 需要实现ActionFilter
- 需要创建异常过滤器
- 需要实现模型验证过滤器
- 需要创建权限控制过滤器
- 需要管理过滤器执行顺序
- 需要优化现有过滤器实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行过滤器设计决策
- 不处理过滤器的深度性能优化（仅设计层面）
- 不负责过滤器的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** ActionFilter实现
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

// 异步ActionFilter
public class TimingAsyncActionFilter : IAsyncActionFilter
{
    private readonly ILogger<TimingAsyncActionFilter> _logger;

    public TimingAsyncActionFilter(ILogger<TimingAsyncActionFilter> logger)
    {
        _logger = logger;
    }

    public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
    {
        // 在Action执行之前执行
        var stopwatch = System.Diagnostics.Stopwatch.StartNew();

        // 执行Action
        var resultContext = await next();

        // 在Action执行之后执行
        stopwatch.Stop();
        var controllerName = context.Controller.GetType().Name;
        var actionName = context.ActionDescriptor.RouteValues["action"];
        _logger.LogInformation("Action {Controller}.{Action} took {ElapsedMilliseconds}ms", 
            controllerName, actionName, stopwatch.ElapsedMilliseconds);
    }
}

// 注册过滤器
// 方式1: 全局注册
builder.Services.AddControllers(options =>
{
    options.Filters.Add<LoggingActionFilter>();
});

// 方式2: 控制器级别
[ServiceFilter(typeof(LoggingActionFilter))]
public class UsersController : ControllerBase
{
    // ...
}

// 方式3: Action级别
[HttpGet]
[ServiceFilter(typeof(TimingAsyncActionFilter))]
public ActionResult<IEnumerable<UserDto>> GetUsers()
{
    // ...
}
```

**Pattern 2:** 异常过滤器
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
        else if (context.Exception is BadRequestException)
        {
            context.Result = new BadRequestObjectResult(new
            {
                Message = context.Exception.Message,
                StatusCode = 400
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

// 注册异常过滤器
builder.Services.AddControllers(options =>
{
    options.Filters.Add<ExceptionFilter>();
});
```

**Pattern 3:** 模型验证过滤器
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

// 注册模型验证过滤器
builder.Services.AddControllers(options =>
{
    options.Filters.Add<CustomModelValidationFilter>();
});
```

**Pattern 4:** 权限控制过滤器
```csharp
// 权限控制过滤器
public class AuthorizationFilter : IAuthorizationFilter
{
    private readonly string _requiredRole;

    public AuthorizationFilter(string requiredRole)
    {
        _requiredRole = requiredRole;
    }

    public void OnAuthorization(AuthorizationFilterContext context)
    {
        // 检查用户是否已认证
        if (!context.HttpContext.User.Identity.IsAuthenticated)
        {
            context.Result = new UnauthorizedResult();
            return;
        }

        // 检查用户是否具有所需角色
        if (!context.HttpContext.User.IsInRole(_requiredRole))
        {
            context.Result = new ForbidResult();
            return;
        }

        // 权限检查通过，继续执行
    }
}

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

**Pattern 5:** 过滤器执行顺序
```csharp
// 过滤器执行顺序示例

// 1. 授权过滤器 (Authorization filters)
//    - IAuthorizationFilter
//    - IAsyncAuthorizationFilter

// 2. 资源过滤器 (Resource filters)
//    - IResourceFilter
//    - IAsyncResourceFilter

// 3. 动作过滤器 (Action filters)
//    - IActionFilter
//    - IAsyncActionFilter
//    - IResultFilter
//    - IAsyncResultFilter

// 4. 异常过滤器 (Exception filters)
//    - IExceptionFilter
//    - IAsyncExceptionFilter

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

## Examples

### Example 1: 实现ActionFilter
- Input: 需要创建一个记录Action执行信息的过滤器
- Steps:
  1. 创建实现IActionFilter或IAsyncActionFilter的类
  2. 实现OnActionExecuting和OnActionExecuted方法
  3. 注册过滤器（全局、控制器级别或Action级别）
  4. 测试过滤器是否正确执行
- Expected output / acceptance: ActionFilter正确记录Action执行信息

### Example 2: 创建异常过滤器
- Input: 需要创建一个统一处理异常的过滤器
- Steps:
  1. 创建实现IExceptionFilter的类
  2. 实现OnException方法处理异常
  3. 为不同类型的异常返回不同的响应
  4. 注册异常过滤器
  5. 测试异常处理是否正确
- Expected output / acceptance: 异常过滤器正确处理和响应异常

### Example 3: 创建权限控制过滤器
- Input: 需要创建一个基于角色的权限控制过滤器
- Steps:
  1. 创建实现IAuthorizationFilter的类
  2. 实现OnAuthorization方法检查用户权限
  3. 创建自定义特性包装过滤器
  4. 在需要权限控制的Action上应用特性
  5. 测试权限控制是否正确
- Expected output / acceptance: 权限控制过滤器正确检查用户权限

## References

- `references/index.md`: 过滤器开发最佳实践导航
- `references/action-filters.md`: ActionFilter实现指南
- `references/exception-filters.md`: 异常过滤器开发指南
- `references/validation-filters.md`: 模型验证过滤器开发指南
- `references/authorization-filters.md`: 权限控制过滤器开发指南
- `references/filter-order.md`: 过滤器执行顺序管理指南

## Maintenance

- Sources: ASP.NET Core官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现