---
name: config-manager
description: "C#配置管理器技能：负责appsettings.json结构化配置、配置绑定(Options模式)、环境变量管理、配置热更新、用户机密管理。使用时需要配置appsettings.json、绑定配置到对象、管理环境变量、实现配置热更新、管理用户机密。"
---

# config-manager Skill

为C#项目提供专业的配置管理能力，确保项目具有灵活、安全、可维护的配置系统。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置appsettings.json结构化配置
- 需要使用Options模式绑定配置到对象
- 需要管理环境变量
- 需要实现配置热更新
- 需要管理用户机密
- 需要优化现有配置系统

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行配置设计
- 不处理配置的加密和解密（除了用户机密）
- 不负责配置服务器的高级配置（仅本地配置）

## Quick Reference

### Common Patterns

**Pattern 1:** 基本配置结构 (appsettings.json)
```json
{
  "AppSettings": {
    "ApplicationName": "MyApp",
    "Version": "1.0.0",
    "Logging": {
      "Level": "Information",
      "File": "logs/app.log"
    },
    "Database": {
      "ConnectionString": "Server=localhost;Database=MyDb;User=sa;Password=P@ssw0rd;"
    }
  }
}
```

**Pattern 2:** Options模式绑定
```csharp
// 创建配置模型
public class AppSettings
{
    public string ApplicationName { get; set; }
    public string Version { get; set; }
    public LoggingSettings Logging { get; set; }
    public DatabaseSettings Database { get; set; }
}

public class LoggingSettings
{
    public string Level { get; set; }
    public string File { get; set; }
}

public class DatabaseSettings
{
    public string ConnectionString { get; set; }
}

// 在Program.cs中注册
builder.Services.Configure<AppSettings>(builder.Configuration.GetSection("AppSettings"));

// 在服务中使用
public class MyService
{
    private readonly AppSettings _appSettings;

    public MyService(IOptions<AppSettings> appSettings)
    {
        _appSettings = appSettings.Value;
    }
}
```

**Pattern 3:** 配置热更新
```csharp
// 注册支持热更新的Options
builder.Services.Configure<AppSettings>(builder.Configuration.GetSection("AppSettings"));

// 或使用IOptionsSnapshot支持范围级别的更新
builder.Services.AddScoped(sp => sp.GetRequiredService<IOptionsSnapshot<AppSettings>>().Value);

// 在服务中使用
public class MyService
{
    private readonly IOptionsMonitor<AppSettings> _appSettingsMonitor;

    public MyService(IOptionsMonitor<AppSettings> appSettingsMonitor)
    {
        _appSettingsMonitor = appSettingsMonitor;
        
        // 监听配置变化
        _appSettingsMonitor.OnChange(settings => {
            Console.WriteLine($"配置已更新: {settings.ApplicationName}");
        });
    }
}
```

**Pattern 4:** 用户机密管理
```bash
# 初始化用户机密
 dotnet user-secrets init

# 添加机密
dotnet user-secrets set "Database:ConnectionString" "Server=localhost;Database=MyDb;User=sa;Password=SecureP@ssw0rd;"
```

```csharp
// 在Program.cs中使用用户机密
if (builder.Environment.IsDevelopment())
{
    builder.Configuration.AddUserSecrets<Program>();
}
```

**Pattern 5:** 环境变量管理
```csharp
// 在Program.cs中添加环境变量配置
builder.Configuration.AddEnvironmentVariables();

// 可以使用前缀过滤环境变量
builder.Configuration.AddEnvironmentVariables(prefix: "MYAPP_");
```

## Examples

### Example 1: 使用Options模式绑定配置
- Input: 需要将appsettings.json中的配置绑定到对象
- Steps:
  1. 创建配置模型类
  2. 在Program.cs中注册配置
  3. 在服务中注入IOptions<AppSettings>
  4. 使用配置值
- Expected output / acceptance: 配置值正确绑定到对象并可以使用

### Example 2: 实现配置热更新
- Input: 需要实现配置热更新功能
- Steps:
  1. 使用IOptionsMonitor注册配置
  2. 在服务中注入IOptionsMonitor
  3. 添加配置变化监听
  4. 修改appsettings.json文件
- Expected output / acceptance: 配置变化时自动更新

### Example 3: 使用用户机密
- Input: 需要管理敏感配置信息
- Steps:
  1. 初始化用户机密
  2. 添加机密信息
  3. 在Program.cs中启用用户机密
  4. 读取机密配置
- Expected output / acceptance: 敏感配置信息被安全管理

## References

- `references/index.md`: 配置管理最佳实践导航
- `references/appsettings-json.md`: appsettings.json配置指南
- `references/options-pattern.md`: Options模式使用指南
- `references/environment-variables.md`: 环境变量管理指南
- `references/user-secrets.md`: 用户机密管理指南

## Maintenance

- Sources: Microsoft官方文档和行业最佳实践
- Last updated: 2026-01-20
- Known limits: 不支持配置服务器的高级配置