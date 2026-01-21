---
name: nuget-manager
description: "C# NuGet管家技能：负责包依赖管理、版本约束策略、PackageReference配置、私有NuGet源、包版本冲突解决。使用时需要管理包依赖、配置版本约束、设置PackageReference、配置私有NuGet源、解决包版本冲突。"
---

# nuget-manager Skill

为C#项目提供专业的NuGet包管理能力，确保项目具有清晰、可控、无冲突的包依赖管理系统。

## When to Use This Skill

Trigger when any of these applies:
- 需要管理项目的包依赖
- 需要配置包的版本约束策略
- 需要设置PackageReference配置
- 需要配置私有NuGet源
- 需要解决包版本冲突
- 需要优化现有包依赖结构

## Not For / Boundaries

- 不负责包的具体功能使用
- 不替代开发人员选择合适的包
- 不处理包的安装和更新（仅配置）
- 不负责包的发布和维护

## Quick Reference

### Common Patterns

**Pattern 1:** 基本PackageReference配置
```xml
<!-- 在.csproj文件中 -->
<ItemGroup>
  <!-- 具体版本 -->
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
  
  <!-- 最低版本 -->
  <PackageReference Include="Microsoft.Extensions.Logging" Version="^6.0.0" />
  
  <!-- 范围版本 -->
  <PackageReference Include="EntityFrameworkCore" Version="[6.0.0, 7.0.0)" />
  
  <!-- 最新版本 -->
  <PackageReference Include="AutoMapper" Version="*" />
</ItemGroup>
```

**Pattern 2:** 私有NuGet源配置（NuGet.config）
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
    <add key="PrivateFeed" value="https://private-feed.example.com/v3/index.json" />
    <add key="LocalFeed" value="../../packages" />
  </packageSources>
  <packageSourceCredentials>
    <PrivateFeed>
      <add key="Username" value="user" />
      <add key="ClearTextPassword" value="password" />
    </PrivateFeed>
  </packageSourceCredentials>
</configuration>
```

**Pattern 3:** 包版本约束策略
```xml
<!-- 在Directory.Build.props文件中 -->
<Project>
  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <!-- 集中管理包版本 -->
    <PackageVersion Include="Newtonsoft.Json" Version="13.0.3" />
    <PackageVersion Include="Microsoft.Extensions.Logging" Version="6.0.0" />
  </ItemGroup>
</Project>

<!-- 在.csproj文件中 -->
<ItemGroup>
  <!-- 使用集中管理的版本 -->
  <PackageReference Include="Newtonsoft.Json" />
  <PackageReference Include="Microsoft.Extensions.Logging" />
</ItemGroup>
```

**Pattern 4:** 解决包版本冲突
```xml
<!-- 在.csproj文件中强制使用特定版本 -->
<ItemGroup>
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
</ItemGroup>
<ItemGroup>
  <!-- 解决版本冲突 -->
  <PackageVersion Include="Newtonsoft.Json" Version="13.0.3" />
</ItemGroup>

<!-- 或使用依赖版本管理 -->
<PropertyGroup>
  <DependencyVersion>Lowest</DependencyVersion>
</PropertyGroup>
```

**Pattern 5:** 使用dotnet CLI管理包
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

# 更新所有包
dotnet outdated --upgrade
```

## Examples

### Example 1: 管理包依赖
- Input: 需要为项目添加和管理包依赖
- Steps:
  1. 分析项目需要的包
  2. 使用dotnet add package命令添加包
  3. 配置合适的版本约束
  4. 验证包是否正确安装
- Expected output / acceptance: 包依赖正确配置并可以使用

### Example 2: 配置私有NuGet源
- Input: 需要为项目配置私有NuGet源
- Steps:
  1. 创建或修改NuGet.config文件
  2. 添加私有NuGet源的URL
  3. 配置源的凭据（如果需要）
  4. 验证源是否可以访问
- Expected output / acceptance: 私有NuGet源正确配置并可以使用

### Example 3: 解决包版本冲突
- Input: 项目存在包版本冲突
- Steps:
  1. 使用dotnet list package命令查看冲突
  2. 分析冲突的原因
  3. 使用PackageVersion或DependencyVersion解决冲突
  4. 验证冲突是否解决
- Expected output / acceptance: 包版本冲突解决，项目可以正常构建

## References

- `references/index.md`: NuGet管理最佳实践导航
- `references/package-reference.md`: PackageReference配置指南
- `references/version-constraints.md`: 版本约束策略指南
- `references/private-feeds.md`: 私有NuGet源配置指南
- `references/conflict-resolution.md`: 包版本冲突解决指南

## Maintenance

- Sources: NuGet官方文档和行业最佳实践
- Last updated: 2026-01-20
- Known limits: 不负责包的具体功能使用和维护