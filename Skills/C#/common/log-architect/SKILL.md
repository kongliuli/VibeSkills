---
name: log-architect
description: "C#日志架构师技能：负责Serilog/NLog集成、日志格式化、多输出目标配置、结构化日志设计、日志级别策略。使用时需要集成Serilog/NLog、配置日志格式化、设置多输出目标、设计结构化日志、制定日志级别策略。"
---

# log-architect Skill

为C#项目提供专业的日志架构设计和配置能力，确保项目具有高效、灵活、可维护的日志系统。

## When to Use This Skill

Trigger when any of these applies:
- 需要集成Serilog/NLog等日志框架
- 需要配置日志格式化
- 需要设置多输出目标
- 需要设计结构化日志
- 需要制定日志级别策略
- 需要优化现有日志系统

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行日志内容设计
- 不处理日志的存储和查询（仅配置）
- 不负责日志分析和监控系统的配置

## Quick Reference

### Common Patterns

**Pattern 1:** Serilog集成
```csharp
// 安装NuGet包: Serilog.AspNetCore

// 在Program.cs中
using Serilog;

var builder = WebApplication.CreateBuilder(args);

// 配置Serilog
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
    .Enrich.FromLogContext()
    .WriteTo.Console()
    .WriteTo.File("logs/log-.txt", rollingInterval: RollingInterval.Day)
    .CreateLogger();

try
{
    Log.Information("Starting web application");
    builder.Host.UseSerilog(); // 使用Serilog替代默认日志
    
    // ... 其他配置
    
    var app = builder.Build();
    app.Run();
}
catch (Exception ex)
{
    Log.Fatal(ex, "Application terminated unexpectedly");
}
finally
{
    Log.CloseAndFlush();
}
```

**Pattern 2:** NLog集成
```csharp
// 安装NuGet包: NLog.Web.AspNetCore

// 在Program.cs中
using NLog.Web;

var logger = NLog.LogManager.Setup().LoadConfigurationFromAppSettings().GetCurrentClassLogger();

try
{
    logger.Info("Starting web application");
    
    var builder = WebApplication.CreateBuilder(args);
    builder.Logging.ClearProviders();
    builder.Host.UseNLog(); // 使用NLog
    
    // ... 其他配置
    
    var app = builder.Build();
    app.Run();
}
catch (Exception ex)
{
    logger.Fatal(ex, "Application terminated unexpectedly");
    throw;
}
finally
{
    NLog.LogManager.Shutdown();
}
```

**Pattern 3:** 结构化日志设计
```csharp
// 使用Serilog记录结构化日志
Log.Information("用户 {UserId} 登录成功，IP地址: {IpAddress}", userId, ipAddress);

// 使用NLog记录结构化日志
logger.Info("用户 {0} 登录成功，IP地址: {1}", userId, ipAddress);

// 使用内置日志记录结构化日志
_logger.LogInformation("用户 {UserId} 登录成功，IP地址: {IpAddress}", userId, ipAddress);
```

**Pattern 4:** 多输出目标配置 (Serilog)
```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .WriteTo.Console(new JsonFormatter()) // 控制台输出JSON格式
    .WriteTo.File("logs/log-.txt", rollingInterval: RollingInterval.Day) // 文件输出
    .WriteTo.Seq("http://localhost:5341") // Seq输出
    .WriteTo.Elasticsearch(new ElasticsearchSinkOptions(new Uri("http://localhost:9200")) // Elasticsearch输出
    {
        AutoRegisterTemplate = true
    })
    .CreateLogger();
```

**Pattern 5:** 日志级别策略
```json
// appsettings.json中的日志级别配置
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "MyApp.Namespace": "Debug"
    }
  }
}
```

## Examples

### Example 1: 集成Serilog
- Input: 需要将Serilog集成到ASP.NET Core项目
- Steps:
  1. 安装Serilog.AspNetCore包
  2. 在Program.cs中配置Serilog
  3. 使用builder.Host.UseSerilog()替代默认日志
  4. 添加异常处理和日志清理
- Expected output / acceptance: Serilog正确集成并记录日志

### Example 2: 设计结构化日志
- Input: 需要为用户操作记录结构化日志
- Steps:
  1. 分析需要记录的关键信息
  2. 使用结构化日志语法记录信息
  3. 确保包含足够的上下文信息
  4. 避免记录敏感信息
- Expected output / acceptance: 结构化日志可以被正确解析和查询

### Example 3: 配置多输出目标
- Input: 需要将日志输出到控制台、文件和Seq
- Steps:
  1. 安装必要的Serilog包
  2. 在Serilog配置中添加多个输出目标
  3. 为不同目标配置不同的格式
  4. 测试日志输出
- Expected output / acceptance: 日志可以同时输出到多个目标

## References

- `references/index.md`: 日志架构最佳实践导航
- `references/serilog-integration.md`: Serilog集成指南
- `references/nlog-integration.md`: NLog集成指南
- `references/structured-logging.md`: 结构化日志设计指南
- `references/log-levels.md`: 日志级别策略指南

## Maintenance

- Sources: Serilog/NLog官方文档和行业最佳实践
- Last updated: 2026-01-20
- Known limits: 不支持日志分析和监控系统的配置