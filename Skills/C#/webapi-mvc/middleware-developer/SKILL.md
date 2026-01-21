---
name: middleware-developer
description: "C#中间件开发者技能：负责自定义中间件编写、请求/响应管道配置、横切关注点处理、中间件顺序管理、性能监控中间件。使用时需要编写自定义中间件、配置请求/响应管道、处理横切关注点、管理中间件顺序、创建性能监控中间件。"
---

# middleware-developer Skill

为C# WebAPI项目提供专业的中间件开发和配置能力，确保请求/响应管道具有高效、灵活、可维护的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要编写自定义中间件
- 需要配置请求/响应管道
- 需要处理横切关注点
- 需要管理中间件顺序
- 需要创建性能监控中间件
- 需要优化现有中间件实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行中间件设计决策
- 不处理中间件的安全性配置（除了基本设计）
- 不负责中间件的深度性能优化（仅设计层面）

## Quick Reference

### Common Patterns

**Pattern 1:** 基本中间件结构
```csharp
// 自定义中间件
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // 在请求处理之前执行的代码
        Console.WriteLine("Middleware: Before handling request");

        // 调用管道中的下一个中间件
        await _next(context);

        // 在请求处理之后执行的代码
        Console.WriteLine("Middleware: After handling request");
    }
}

// 中间件扩展方法
public static class CustomMiddlewareExtensions
{
    public static IApplicationBuilder UseCustomMiddleware(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<CustomMiddleware>();
    }
}

// 在Program.cs中使用
app.UseCustomMiddleware();
```

**Pattern 2:** 带依赖注入的中间件
```csharp
// 带依赖注入的中间件
public class LoggerMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<LoggerMiddleware> _logger;

    public LoggerMiddleware(RequestDelegate next, ILogger<LoggerMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        _logger.LogInformation("Request received: {Path}", context.Request.Path);

        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred while processing the request");
            throw;
        }
        finally
        {
            _logger.LogInformation("Request completed with status code: {StatusCode}", context.Response.StatusCode);
        }
    }
}

// 中间件扩展方法
public static class LoggerMiddlewareExtensions
{
    public static IApplicationBuilder UseLoggerMiddleware(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<LoggerMiddleware>();
    }
}
```

**Pattern 3:** 请求/响应管道配置
```csharp
// 在Program.cs中配置中间件管道
var app = builder.Build();

// 开发环境特定中间件
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseSwagger();
    app.UseSwaggerUI();
}

// 全局中间件
app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();
app.UseAuthentication();
app.UseAuthorization();

// 自定义中间件
app.UseLoggerMiddleware();
app.UseCustomMiddleware();

// 端点路由
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapHealthChecks("/health");
});

app.Run();
```

**Pattern 4:** 性能监控中间件
```csharp
// 性能监控中间件
public class PerformanceMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<PerformanceMiddleware> _logger;

    public PerformanceMiddleware(RequestDelegate next, ILogger<PerformanceMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // 记录开始时间
        var startTime = DateTime.UtcNow;
        var stopwatch = System.Diagnostics.Stopwatch.StartNew();

        try
        {
            // 处理请求
            await _next(context);
        }
        finally
        {
            // 记录结束时间和执行时间
            stopwatch.Stop();
            var endTime = DateTime.UtcNow;
            var elapsedMilliseconds = stopwatch.ElapsedMilliseconds;

            // 记录性能指标
            _logger.LogInformation(
                "Request performance: {Method} {Path} took {ElapsedMilliseconds}ms and returned {StatusCode}",
                context.Request.Method,
                context.Request.Path,
                elapsedMilliseconds,
                context.Response.StatusCode
            );

            // 添加性能头信息
            context.Response.Headers.Add("X-Response-Time", elapsedMilliseconds.ToString());
        }
    }
}

// 中间件扩展方法
public static class PerformanceMiddlewareExtensions
{
    public static IApplicationBuilder UsePerformanceMiddleware(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<PerformanceMiddleware>();
    }
}
```

**Pattern 5:** 中间件顺序管理
```csharp
// 中间件顺序示例 (从外到内)
app.UseHttpsRedirection();          // 1. 首先处理HTTPS重定向
app.UseStaticFiles();               // 2. 然后处理静态文件
app.UseRouting();                   // 3. 然后进行路由匹配
app.UseAuthentication();            // 4. 然后进行身份验证
app.UseAuthorization();             // 5. 然后进行授权
app.UseSession();                   // 6. 然后处理会话
app.UseCustomMiddleware();          // 7. 然后处理自定义逻辑
app.UseEndpoints(endpoints =>       // 8. 最后处理端点
{
    endpoints.MapControllers();
});

// 注意事项：
// - 认证中间件必须在授权中间件之前
// - 路由中间件必须在使用路由值的中间件之前
// - 静态文件中间件应该在其他中间件之前（除了HTTPS重定向）
// - 自定义中间件的顺序取决于其依赖关系
```

## Examples

### Example 1: 编写基本自定义中间件
- Input: 需要创建一个记录请求信息的中间件
- Steps:
  1. 创建自定义中间件类
  2. 实现InvokeAsync方法
  3. 在方法中添加请求前和请求后的逻辑
  4. 创建中间件扩展方法
  5. 在Program.cs中注册中间件
- Expected output / acceptance: 中间件正确记录请求信息

### Example 2: 配置请求/响应管道
- Input: 需要为WebAPI项目配置完整的中间件管道
- Steps:
  1. 分析项目需要的中间件
  2. 按照正确的顺序配置中间件
  3. 添加环境特定的中间件
  4. 配置端点路由
  5. 测试管道配置
- Expected output / acceptance: 中间件管道正确配置并工作

### Example 3: 创建性能监控中间件
- Input: 需要创建一个监控请求执行时间的中间件
- Steps:
  1. 创建性能监控中间件类
  2. 在InvokeAsync方法中记录开始和结束时间
  3. 计算执行时间并记录日志
  4. 可选：添加响应头信息
  5. 在Program.cs中注册中间件
- Expected output / acceptance: 中间件正确记录请求执行时间

## References

- `references/index.md`: 中间件开发最佳实践导航
- `references/basic-middleware.md`: 基本中间件开发指南
- `references/pipeline-configuration.md`: 请求/响应管道配置指南
- `references/performance-middleware.md`: 性能监控中间件开发指南
- `references/middleware-order.md`: 中间件顺序管理指南

## Maintenance

- Sources: ASP.NET Core官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现