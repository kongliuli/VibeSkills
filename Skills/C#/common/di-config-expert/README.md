# DI配置专家 (di-config-expert)

为C#项目提供专业的依赖注入(DI)配置和管理能力，确保项目具有清晰、可维护的服务注册和生命周期管理。

## 主要功能

- **服务注册**：在Startup/Program.cs中注册服务
- **生命周期管理**：管理Scoped/Transient/Singleton生命周期
- **工厂模式注册**：使用工厂方法或工厂类注册服务
- **第三方容器集成**：集成Autofac等第三方DI容器
- **批量注册**：批量注册服务以提高效率

## 使用场景

- 需要在Startup/Program.cs中注册服务
- 需要管理服务生命周期
- 需要使用工厂模式注册服务
- 需要集成第三方DI容器
- 需要优化现有DI配置
- 需要解决服务解析问题

## 快速开始

### 基本服务注册
```csharp
// 在Program.cs或Startup.ConfigureServices中
builder.Services.AddTransient<IMyService, MyService>();
builder.Services.AddScoped<IAnotherService, AnotherService>();
builder.Services.AddSingleton<IThirdService, ThirdService>();
```

### 工厂模式注册
```csharp
// 使用工厂方法注册服务
builder.Services.AddTransient<IMyService>(provider => {
    var dependency = provider.GetRequiredService<IDependency>();
    return new MyService(dependency, "custom parameter");
});
```

### 第三方容器集成 (Autofac)
```csharp
// 安装NuGet包: Autofac.Extensions.DependencyInjection

// 在Program.cs中
builder.Host.UseServiceProviderFactory(new AutofacServiceProviderFactory());
builder.Host.ConfigureContainer<ContainerBuilder>(builder => {
    builder.RegisterType<MyService>().As<IMyService>();
    builder.RegisterType<AnotherService>().As<IAnotherService>().InstancePerLifetimeScope();
});
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件