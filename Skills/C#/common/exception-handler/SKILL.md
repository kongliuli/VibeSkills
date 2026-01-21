---
name: exception-handler
description: "C#异常处理专家技能：负责全局异常中间件、自定义异常类设计、错误码规范、异常日志记录、用户友好错误信息。使用时需要创建全局异常中间件、设计自定义异常类、制定错误码规范、记录异常日志、生成用户友好错误信息。"
---

# exception-handler Skill

为C#项目提供专业的异常处理设计和实现能力，确保项目具有统一、安全、用户友好的异常处理系统。

## When to Use This Skill

Trigger when any of these applies:
- 需要创建全局异常中间件
- 需要设计自定义异常类
- 需要制定错误码规范
- 需要记录异常日志
- 需要生成用户友好错误信息
- 需要优化现有异常处理系统

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行异常类型设计
- 不处理业务逻辑错误的具体内容
- 不负责错误监控和告警系统的配置

## Quick Reference

### Common Patterns

**Pattern 1:** 全局异常中间件
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

        var statusCode = HttpStatusCode.InternalServerError;
        var message = "Internal server error";
        var errorCode = "SERVER_ERROR";

        if (ex is BadRequestException badRequestEx)
        {
            statusCode = HttpStatusCode.BadRequest;
            message = badRequestEx.Message;
            errorCode = badRequestEx.ErrorCode;
        }
        else if (ex is NotFoundException notFoundEx)
        {
            statusCode = HttpStatusCode.NotFound;
            message = notFoundEx.Message;
            errorCode = notFoundEx.ErrorCode;
        }
        // 其他异常类型...

        context.Response.ContentType = "application/json";
        context.Response.StatusCode = (int)statusCode;

        var response = new ErrorResponse
        {
            StatusCode = (int)statusCode,
            Message = message,
            ErrorCode = errorCode,
            TraceId = context.TraceIdentifier
        };

        await context.Response.WriteAsJsonAsync(response);
    }
}

// 注册中间件
public static class ExceptionHandlingMiddlewareExtensions
{
    public static IApplicationBuilder UseExceptionHandling(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<ExceptionHandlingMiddleware>();
    }
}

// 在Program.cs中使用
app.UseExceptionHandling();
```

**Pattern 2:** 自定义异常类设计
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
    public BadRequestException(string message, string errorCode) : base(message, errorCode) { }
}

public class NotFoundException : CustomException
{
    public NotFoundException(string message) : base(message, "NOT_FOUND") { }
    public NotFoundException(string message, string errorCode) : base(message, errorCode) { }
}

public class UnauthorizedException : CustomException
{
    public UnauthorizedException(string message) : base(message, "UNAUTHORIZED") { }
    public UnauthorizedException(string message, string errorCode) : base(message, errorCode) { }
}

public class ForbiddenException : CustomException
{
    public ForbiddenException(string message) : base(message, "FORBIDDEN") { }
    public ForbiddenException(string message, string errorCode) : base(message, errorCode) { }
}
```

**Pattern 3:** 错误码规范
```csharp
// 错误码枚举或静态类
public static class ErrorCodes
{
    // 系统错误
    public const string SERVER_ERROR = "SERVER_ERROR";
    public const string DATABASE_ERROR = "DATABASE_ERROR";
    
    // 客户端错误
    public const string BAD_REQUEST = "BAD_REQUEST";
    public const string NOT_FOUND = "NOT_FOUND";
    public const string UNAUTHORIZED = "UNAUTHORIZED";
    public const string FORBIDDEN = "FORBIDDEN";
    
    // 业务错误
    public const string USER_NOT_FOUND = "USER_NOT_FOUND";
    public const string INVALID_EMAIL_FORMAT = "INVALID_EMAIL_FORMAT";
    public const string PASSWORD_TOO_WEAK = "PASSWORD_TOO_WEAK";
    public const string RESOURCE_ALREADY_EXISTS = "RESOURCE_ALREADY_EXISTS";
}
```

**Pattern 4:** 错误响应模型
```csharp
// 统一错误响应模型
public class ErrorResponse
{
    public int StatusCode { get; set; }
    public string Message { get; set; }
    public string ErrorCode { get; set; }
    public string TraceId { get; set; }
    public IDictionary<string, string[]> ValidationErrors { get; set; }

    public ErrorResponse() { }

    public ErrorResponse(int statusCode, string message, string errorCode, string traceId)
    {
        StatusCode = statusCode;
        Message = message;
        ErrorCode = errorCode;
        TraceId = traceId;
    }

    public ErrorResponse(int statusCode, string message, string errorCode, string traceId, IDictionary<string, string[]> validationErrors)
        : this(statusCode, message, errorCode, traceId)
    {
        ValidationErrors = validationErrors;
    }
}
```

**Pattern 5:** 异常日志记录
```csharp
// 在服务中记录异常日志
public class UserService : IUserService
{
    private readonly IUserRepository _userRepository;
    private readonly ILogger<UserService> _logger;

    public UserService(IUserRepository userRepository, ILogger<UserService> logger)
    {
        _userRepository = userRepository;
        _logger = logger;
    }

    public async Task<User> GetUserByIdAsync(int userId)
    {
        try
        {
            var user = await _userRepository.GetByIdAsync(userId);
            if (user == null)
            {
                _logger.LogWarning("User with ID {UserId} not found.", userId);
                throw new NotFoundException("User not found", ErrorCodes.USER_NOT_FOUND);
            }
            return user;
        }
        catch (NotFoundException)
        {
            throw; // 重新抛出已知异常
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting user with ID {UserId}.", userId);
            throw new CustomException("An error occurred while getting the user.", ErrorCodes.SERVER_ERROR);
        }
    }
}
```

## Examples

### Example 1: 创建全局异常中间件
- Input: 需要为ASP.NET Core项目创建全局异常中间件
- Steps:
  1. 创建ExceptionHandlingMiddleware类
  2. 实现InvokeAsync方法处理异常
  3. 创建扩展方法注册中间件
  4. 在Program.cs中使用中间件
- Expected output / acceptance: 全局异常中间件正确处理所有未捕获的异常

### Example 2: 设计自定义异常类
- Input: 需要为项目设计自定义异常类
- Steps:
  1. 创建基础CustomException类
  2. 继承创建具体异常类型（BadRequestException、NotFoundException等）
  3. 为每个异常类型设置默认错误码
  4. 在业务逻辑中使用自定义异常
- Expected output / acceptance: 自定义异常类可以正确使用并提供错误码信息

### Example 3: 制定错误码规范
- Input: 需要为项目制定错误码规范
- Steps:
  1. 分析项目的错误类型（系统错误、客户端错误、业务错误等）
  2. 创建ErrorCodes静态类定义错误码
  3. 为每种错误类型分配唯一的错误码
  4. 在异常和错误响应中使用错误码
- Expected output / acceptance: 项目使用统一的错误码规范

## References

- `references/index.md`: 异常处理最佳实践导航
- `references/global-exception-middleware.md`: 全局异常中间件设计指南
- `references/custom-exceptions.md`: 自定义异常类设计指南
- `references/error-codes.md`: 错误码规范指南
- `references/error-responses.md`: 错误响应模型设计指南

## Maintenance

- Sources: ASP.NET Core官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责错误监控和告警系统的配置