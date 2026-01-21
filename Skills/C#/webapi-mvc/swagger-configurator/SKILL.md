---
name: swagger-configurator
description: "C# Swagger配置师技能：负责OpenAPI文档配置、JWT认证标注、响应示例生成、多版本API文档、自定义Swagger UI。使用时需要配置OpenAPI文档、添加JWT认证标注、生成响应示例、配置多版本API文档、自定义Swagger UI。"
---

# swagger-configurator Skill

为C# WebAPI项目提供专业的Swagger/OpenAPI配置能力，确保API文档具有完整、规范、易用的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置OpenAPI文档
- 需要添加JWT认证标注
- 需要生成响应示例
- 需要配置多版本API文档
- 需要自定义Swagger UI
- 需要优化现有Swagger配置

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行API设计决策
- 不处理Swagger的深度性能优化（仅配置层面）
- 不负责Swagger的测试和调试（仅配置）

## Quick Reference

### Common Patterns

**Pattern 1:** 基本Swagger配置
```csharp
// 在Program.cs中配置Swagger
var builder = WebApplication.CreateBuilder(args);

// 添加Swagger生成器
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "API",
        Description = "API文档",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Contact",
            Url = new Uri("https://example.com/contact")
        },
        License = new OpenApiLicense
        {
            Name = "License",
            Url = new Uri("https://example.com/license")
        }
    });

    // 包含XML注释
    var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
    var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
    options.IncludeXmlComments(xmlPath);
});

var app = builder.Build();

// 配置Swagger UI
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(options =>
    {
        options.SwaggerEndpoint("/swagger/v1/swagger.json", "API v1");
        options.RoutePrefix = "swagger";
    });
}

app.Run();
```

**Pattern 2:** JWT认证标注
```csharp
// 配置JWT认证标注
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo { Title = "API", Version = "v1" });

    // 添加JWT认证标注
    var securityScheme = new OpenApiSecurityScheme
    {
        Name = "Authorization",
        Type = SecuritySchemeType.Http,
        Scheme = "bearer",
        BearerFormat = "JWT",
        In = ParameterLocation.Header,
        Description = "Enter 'Bearer' [space] and then your valid token in the text input below.\n\nExample: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
    };

    options.AddSecurityDefinition("Bearer", securityScheme);

    var securityRequirement = new OpenApiSecurityRequirement
    {
        {
            new OpenApiSecurityScheme
            {
                Reference = new OpenApiReference
                {
                    Type = ReferenceType.SecurityScheme,
                    Id = "Bearer"
                }
            },
            new string[] {}
        }
    };

    options.AddSecurityRequirement(securityRequirement);
});
```

**Pattern 3:** 响应示例生成
```csharp
// 配置响应示例
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo { Title = "API", Version = "v1" });

    // 使用Swashbuckle.AspNetCore.Filters包添加响应示例
    options.ExampleFilters();
});

// 安装包: Install-Package Swashbuckle.AspNetCore.Filters

// 添加示例类
public class UserExample : IExamplesProvider<UserDto>
{
    public UserDto GetExamples()
    {
        return new UserDto
        {
            Id = 1,
            Name = "John Doe",
            Email = "john.doe@example.com",
            Age = 30
        };
    }
}

// 在控制器中使用
[HttpGet("{id}")]
[ProducesResponseType(typeof(UserDto), 200)]
[SwaggerResponseExample(200, typeof(UserExample))]
public ActionResult<UserDto> GetUser(int id)
{
    // 实现
}
```

**Pattern 4:** 多版本API文档
```csharp
// 配置多版本API文档
builder.Services.AddSwaggerGen(options =>
{
    // v1版本
    options.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "API v1",
        Description = "API v1文档"
    });

    // v2版本
    options.SwaggerDoc("v2", new OpenApiInfo
    {
        Version = "v2",
        Title = "API v2",
        Description = "API v2文档"
    });

    // 包含XML注释
    var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
    var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
    options.IncludeXmlComments(xmlPath);

    // 配置API版本选择器
    options.DocInclusionPredicate((docName, apiDesc) =>
    {
        var actionApiVersionModel = apiDesc.ActionDescriptor.EndpointMetadata
            .OfType<ApiVersionAttribute>()
            .FirstOrDefault();

        // 如果没有版本属性，则默认为v1
        if (actionApiVersionModel == null)
        {
            return docName == "v1";
        }

        // 匹配版本
        return actionApiVersionModel.Versions.Any(v => v.ToString() == docName);
    });

    // 配置路由模板
    options.TagActionsBy(api => new[] { api.GroupName ?? "v1" });
});

// 配置Swagger UI
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/swagger/v1/swagger.json", "API v1");
    options.SwaggerEndpoint("/swagger/v2/swagger.json", "API v2");
    options.RoutePrefix = "swagger";
});

// 控制器版本标注
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
[ApiVersion("2.0")]
public class UsersController : ControllerBase
{
    [HttpGet]
    [MapToApiVersion("1.0")]
    public ActionResult<IEnumerable<UserDto>> GetV1()
    {
        // v1实现
    }

    [HttpGet]
    [MapToApiVersion("2.0")]
    public ActionResult<IEnumerable<UserDto>> GetV2()
    {
        // v2实现
    }
}
```

**Pattern 5:** 自定义Swagger UI
```csharp
// 自定义Swagger UI
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/swagger/v1/swagger.json", "API v1");
    options.RoutePrefix = "swagger";

    // 自定义UI配置
    options.DocumentTitle = "API Documentation";
    options.DocExpansion(DocExpansion.List);
    options.DefaultModelsExpandDepth(-1); // 隐藏Models部分
    options.DefaultModelExpandDepth(2);
    options.DefaultModelRendering(ModelRendering.Model);
    options.EnableDeepLinking();
    options.EnableFilter();
    options.MaxDisplayedTags(5);
    options.ShowExtensions();
    options.ShowCommonExtensions();

    // 自定义CSS
    options.InjectStylesheet("/swagger-ui/custom.css");

    // 自定义JavaScript
    options.InjectJavascript("/swagger-ui/custom.js");
});

// 添加静态文件服务
app.UseStaticFiles();

// 在wwwroot/swagger-ui/目录下添加custom.css和custom.js文件
```

## Examples

### Example 1: 配置基本Swagger
- Input: 需要为WebAPI项目配置基本的Swagger文档
- Steps:
  1. 添加Swagger生成器服务
  2. 配置Swagger文档信息
  3. 包含XML注释
  4. 配置Swagger UI
  5. 测试Swagger文档
- Expected output / acceptance: Swagger文档正确生成并可访问

### Example 2: 添加JWT认证标注
- Input: 需要为Swagger文档添加JWT认证标注
- Steps:
  1. 配置Swagger生成器
  2. 添加JWT认证安全方案
  3. 添加安全要求
  4. 测试认证标注
- Expected output / acceptance: Swagger文档正确显示JWT认证标注

### Example 3: 配置多版本API文档
- Input: 需要为WebAPI项目配置多版本API文档
- Steps:
  1. 添加Swagger生成器服务
  2. 配置多个Swagger文档版本
  3. 配置API版本选择器
  4. 在控制器中添加版本标注
  5. 测试多版本API文档
- Expected output / acceptance: 多版本API文档正确生成并可访问

## References

- `references/index.md`: Swagger配置最佳实践导航
- `references/basic-config.md`: 基本Swagger配置指南
- `references/jwt-auth.md`: JWT认证标注指南
- `references/response-examples.md`: 响应示例生成指南
- `references/multi-version.md`: 多版本API文档配置指南
- `references/custom-ui.md`: 自定义Swagger UI指南

## Maintenance

- Sources: ASP.NET Core官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现