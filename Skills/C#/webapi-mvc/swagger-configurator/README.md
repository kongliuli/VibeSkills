# Swagger配置师 (swagger-configurator)

为C# WebAPI项目提供专业的Swagger/OpenAPI配置能力，确保API文档具有完整、规范、易用的实现。

## 主要功能

- **OpenAPI文档配置**：配置和生成OpenAPI文档
- **JWT认证标注**：添加JWT认证标注和配置
- **响应示例生成**：生成API响应示例
- **多版本API文档**：配置和管理多版本API文档
- **自定义Swagger UI**：自定义Swagger UI界面和行为

## 使用场景

- 需要配置OpenAPI文档
- 需要添加JWT认证标注
- 需要生成响应示例
- 需要配置多版本API文档
- 需要自定义Swagger UI
- 需要优化现有Swagger配置

## 快速开始

### 基本Swagger配置
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

### JWT认证标注
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

### 响应示例生成
```csharp
// 配置响应示例
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo { Title = "API", Version = "v1" });

    // 使用Swashbuckle.AspNetCore.Filters包添加响应示例
    options.ExampleFilters();
});

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

### 多版本API文档
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
});

// 配置Swagger UI
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/swagger/v1/swagger.json", "API v1");
    options.SwaggerEndpoint("/swagger/v2/swagger.json", "API v2");
    options.RoutePrefix = "swagger";
});
```

### 自定义Swagger UI
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
    options.EnableDeepLinking();
    options.EnableFilter();

    // 自定义CSS
    options.InjectStylesheet("/swagger-ui/custom.css");

    // 自定义JavaScript
    options.InjectJavascript("/swagger-ui/custom.js");
});
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件