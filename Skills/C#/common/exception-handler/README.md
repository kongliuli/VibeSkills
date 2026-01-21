# 异常处理专家 (exception-handler)

为C#项目提供专业的异常处理设计和实现能力，确保项目具有统一、安全、用户友好的异常处理系统。

## 主要功能

- **全局异常中间件**：创建统一的全局异常处理中间件
- **自定义异常类设计**：设计和实现自定义异常类
- **错误码规范**：制定和应用错误码规范
- **异常日志记录**：记录详细的异常日志
- **用户友好错误信息**：生成用户友好的错误信息

## 使用场景

- 需要创建全局异常中间件
- 需要设计自定义异常类
- 需要制定错误码规范
- 需要记录异常日志
- 需要生成用户友好错误信息
- 需要优化现有异常处理系统

## 快速开始

### 全局异常中间件
```csharp
// 创建全局异常中间件
public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionHandlingMiddleware> _logger;

    public ExceptionHandlingMiddleware(RequestDelegate next, ILogger<ExceptionHandlingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            await HandleExceptionAsync(context, ex);
        }
    }

    private async Task HandleExceptionAsync(HttpContext context, Exception ex)
    {
        _logger.LogError(ex, "An unhandled exception occurred.");
        // 处理异常并返回错误响应...
    }
}

// 在Program.cs中使用
app.UseExceptionHandling();
```

### 自定义异常类
```csharp
// 基础自定义异常
public abstract class CustomException : Exception
{
    public string ErrorCode { get; }

    protected CustomException(string message, string errorCode) : base(message)
    {
        ErrorCode = errorCode;
    }
}

// 具体异常类型
public class BadRequestException : CustomException
{
    public BadRequestException(string message) : base(message, "BAD_REQUEST") { }
}

public class NotFoundException : CustomException
{
    public NotFoundException(string message) : base(message, "NOT_FOUND") { }
}
```

### 错误码规范
```csharp
// 错误码静态类
public static class ErrorCodes
{
    // 系统错误
    public const string SERVER_ERROR = "SERVER_ERROR";
    
    // 客户端错误
    public const string BAD_REQUEST = "BAD_REQUEST";
    public const string NOT_FOUND = "NOT_FOUND";
    
    // 业务错误
    public const string USER_NOT_FOUND = "USER_NOT_FOUND";
    public const string INVALID_EMAIL_FORMAT = "INVALID_EMAIL_FORMAT";
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件