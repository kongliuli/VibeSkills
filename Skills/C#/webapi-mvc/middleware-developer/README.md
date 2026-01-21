# 中间件开发者 (middleware-developer)

为C# WebAPI项目提供专业的中间件开发和配置能力，确保请求/响应管道具有高效、灵活、可维护的实现。

## 主要功能

- **自定义中间件编写**：编写符合规范的自定义中间件
- **请求/响应管道配置**：配置和管理请求/响应处理管道
- **横切关注点处理**：处理日志、认证、授权等横切关注点
- **中间件顺序管理**：管理中间件的执行顺序
- **性能监控中间件**：创建和配置性能监控中间件

## 使用场景

- 需要编写自定义中间件
- 需要配置请求/响应管道
- 需要处理横切关注点
- 需要管理中间件顺序
- 需要创建性能监控中间件
- 需要优化现有中间件实现

## 快速开始

### 基本中间件结构
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

### 带依赖注入的中间件
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
```

### 请求/响应管道配置
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

### 性能监控中间件
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
        }
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件