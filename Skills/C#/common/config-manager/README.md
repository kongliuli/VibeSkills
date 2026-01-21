# 配置管理器 (config-manager)

为C#项目提供专业的配置管理能力，确保项目具有灵活、安全、可维护的配置系统。

## 主要功能

- **结构化配置**：配置appsettings.json结构化配置
- **配置绑定**：使用Options模式绑定配置到对象
- **环境变量管理**：管理和使用环境变量
- **配置热更新**：实现配置的热更新功能
- **用户机密管理**：安全管理敏感配置信息

## 使用场景

- 需要配置appsettings.json结构化配置
- 需要使用Options模式绑定配置到对象
- 需要管理环境变量
- 需要实现配置热更新
- 需要管理用户机密
- 需要优化现有配置系统

## 快速开始

### 基本配置结构 (appsettings.json)
```json
{
  "AppSettings": {
    "ApplicationName": "MyApp",
    "Version": "1.0.0",
    "Database": {
      "ConnectionString": "Server=localhost;Database=MyDb;User=sa;Password=P@ssw0rd;"
    }
  }
}
```

### Options模式绑定
```csharp
// 创建配置模型
public class AppSettings
{
    public string ApplicationName { get; set; }
    public string Version { get; set; }
    public DatabaseSettings Database { get; set; }
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

### 配置热更新
```csharp
// 在服务中使用IOptionsMonitor监听配置变化
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

### 用户机密管理
```bash
# 初始化用户机密
 dotnet user-secrets init

# 添加机密
dotnet user-secrets set "Database:ConnectionString" "Server=localhost;Database=MyDb;User=sa;Password=SecureP@ssw0rd;"
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件