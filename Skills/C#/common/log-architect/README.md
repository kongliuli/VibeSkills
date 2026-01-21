# 日志架构师 (log-architect)

为C#项目提供专业的日志架构设计和配置能力，确保项目具有高效、灵活、可维护的日志系统。

## 主要功能

- **日志框架集成**：集成Serilog/NLog等日志框架
- **日志格式化**：配置日志输出格式
- **多输出目标配置**：设置控制台、文件、Seq、Elasticsearch等多输出目标
- **结构化日志设计**：设计和实现结构化日志
- **日志级别策略**：制定和配置日志级别策略

## 使用场景

- 需要集成Serilog/NLog等日志框架
- 需要配置日志格式化
- 需要设置多输出目标
- 需要设计结构化日志
- 需要制定日志级别策略
- 需要优化现有日志系统

## 快速开始

### Serilog集成
```csharp
// 安装NuGet包: Serilog.AspNetCore

// 在Program.cs中
using Serilog;

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

### 结构化日志设计
```csharp
// 使用结构化日志语法记录信息
Log.Information("用户 {UserId} 登录成功，IP地址: {IpAddress}", userId, ipAddress);

// 内置日志记录结构化日志
_logger.LogInformation("用户 {UserId} 登录成功，IP地址: {IpAddress}", userId, ipAddress);
```

### 多输出目标配置
```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .WriteTo.Console(new JsonFormatter()) // 控制台输出JSON格式
    .WriteTo.File("logs/log-.txt", rollingInterval: RollingInterval.Day) // 文件输出
    .WriteTo.Seq("http://localhost:5341") // Seq输出
    .CreateLogger();
```

### 日志级别策略
```json
// appsettings.json中的日志级别配置
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "MyApp.Namespace": "Debug"
    }
  }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件