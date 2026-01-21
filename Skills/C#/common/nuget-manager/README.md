# NuGet管家 (nuget-manager)

为C#项目提供专业的NuGet包管理能力，确保项目具有清晰、可控、无冲突的包依赖管理系统。

## 主要功能

- **包依赖管理**：管理项目的NuGet包依赖
- **版本约束策略**：配置包的版本约束策略
- **PackageReference配置**：设置和优化PackageReference配置
- **私有NuGet源**：配置和管理私有NuGet源
- **包版本冲突解决**：解决和避免包版本冲突

## 使用场景

- 需要管理项目的包依赖
- 需要配置包的版本约束策略
- 需要设置PackageReference配置
- 需要配置私有NuGet源
- 需要解决包版本冲突
- 需要优化现有包依赖结构

## 快速开始

### 基本PackageReference配置
```xml
<!-- 在.csproj文件中 -->
<ItemGroup>
  <!-- 具体版本 -->
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
  
  <!-- 最低版本 -->
  <PackageReference Include="Microsoft.Extensions.Logging" Version="^6.0.0" />
  
  <!-- 范围版本 -->
  <PackageReference Include="EntityFrameworkCore" Version="[6.0.0, 7.0.0)" />
</ItemGroup>
```

### 私有NuGet源配置
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
    <add key="PrivateFeed" value="https://private-feed.example.com/v3/index.json" />
  </packageSources>
  <packageSourceCredentials>
    <PrivateFeed>
      <add key="Username" value="user" />
      <add key="ClearTextPassword" value="password" />
    </PrivateFeed>
  </packageSourceCredentials>
</configuration>
```

### 使用dotnet CLI管理包
```bash
# 安装包
dotnet add package Newtonsoft.Json

# 安装特定版本
dotnet add package Newtonsoft.Json --version 13.0.3

# 更新包
dotnet add package Newtonsoft.Json --version ^13.0.0

# 移除包
dotnet remove package Newtonsoft.Json

# 列出包
dotnet list package

# 列出可更新的包
dotnet list package --outdated
```

### 解决包版本冲突
```xml
<!-- 在.csproj文件中强制使用特定版本 -->
<ItemGroup>
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
</ItemGroup>
<ItemGroup>
  <!-- 解决版本冲突 -->
  <PackageVersion Include="Newtonsoft.Json" Version="13.0.3" />
</ItemGroup>
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件