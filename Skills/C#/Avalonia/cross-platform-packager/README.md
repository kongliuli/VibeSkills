# Avalonia跨平台打包器 (cross-platform-packager)

为C# Avalonia项目提供专业的跨平台打包和发布能力，确保应用能够在不同平台上正确打包和分发。

## 主要功能

- **MSIX包创建**：为Windows平台创建MSIX安装包
- **AppImage生成**：为Linux平台生成AppImage可执行文件
- **DMG打包**：为macOS平台打包DMG安装文件
- **发布配置管理**：管理不同平台的发布配置
- **运行时平台检测**：检测和适配不同运行时平台

## 使用场景

- 需要创建MSIX包
- 需要生成AppImage
- 需要打包DMG
- 需要管理发布配置
- 需要检测运行时平台
- 需要优化现有打包实现

## 快速开始

### MSIX包创建
```xml
<!-- 在项目文件(.csproj)中添加 -->
<PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows</TargetFramework>
    <GenerateAppxPackageOnBuild>true</GenerateAppxPackageOnBuild>
    <AppxPackageSigningEnabled>true</AppxPackageSigningEnabled>
</PropertyGroup>

<!-- 使用Avalonia MSIX打包工具 -->
<ItemGroup>
    <PackageReference Include="Avalonia.Desktop" Version="11.0.0" />
    <PackageReference Include="Avalonia.MSIX" Version="11.0.0" />
</ItemGroup>
```

### AppImage生成
```bash
# AppImage生成脚本
#!/bin/bash

# 设置变量
APP_NAME="MyApp"
APP_VERSION="1.0.0"
OUTPUT_DIR="output"
APP_DIR="$OUTPUT_DIR/$APP_NAME.AppDir"

# 创建目录结构
mkdir -p "$APP_DIR/usr/bin"
mkdir -p "$APP_DIR/usr/share/applications"
mkdir -p "$APP_DIR/usr/share/icons/hicolor/256x256/apps"

# 复制应用文件
dotnet publish -c Release -r linux-x64 --self-contained true -p:PublishSingleFile=true
cp -r bin/Release/net8.0/linux-x64/publish/* "$APP_DIR/usr/bin/"

# 创建.desktop文件
cat > "$APP_DIR/$APP_NAME.desktop" << EOF
[Desktop Entry]
Name=$APP_NAME
Exec=$APP_NAME
Icon=$APP_NAME
Type=Application
Categories=Utility;
EOF

# 生成AppImage
wget -O "$OUTPUT_DIR/appimagetool" "https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-x86_64.AppImage"
chmod +x "$OUTPUT_DIR/appimagetool"
"$OUTPUT_DIR/appimagetool" "$APP_DIR" "$OUTPUT_DIR/$APP_NAME-$APP_VERSION-x86_64.AppImage"
```

### DMG打包
```bash
# DMG打包脚本
#!/bin/bash

# 设置变量
APP_NAME="MyApp"
APP_VERSION="1.0.0"
OUTPUT_DIR="output"
DMG_TEMP="$OUTPUT_DIR/temp"

# 创建目录
mkdir -p "$DMG_TEMP"

# 构建应用
dotnet publish -c Release -r osx-x64 --self-contained true -p:PublishSingleFile=true
cp -r bin/Release/net8.0/osx-x64/publish/$APP_NAME.app "$DMG_TEMP/"

# 创建DMG
hdiutil create -srcfolder "$DMG_TEMP" -volname "$APP_NAME" -format UDRW -size 100m "$OUTPUT_DIR/temp.dmg"
hdiutil convert "$OUTPUT_DIR/temp.dmg" -format UDZO -o "$OUTPUT_DIR/$APP_NAME-$APP_VERSION.dmg"

# 清理
rm "$OUTPUT_DIR/temp.dmg"
rm -rf "$DMG_TEMP"
```

### 发布配置管理
```xml
<!-- win10-x64.pubxml -->
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration>Release</Configuration>
    <PublishDir>bin\Release\net8.0-windows\publish\win10-x64</PublishDir>
    <RuntimeIdentifier>win10-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
  </PropertyGroup>
</Project>

<!-- linux-x64.pubxml -->
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration>Release</Configuration>
    <PublishDir>bin\Release\net8.0\publish\linux-x64</PublishDir>
    <RuntimeIdentifier>linux-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
  </PropertyGroup>
</Project>

<!-- osx-x64.pubxml -->
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration>Release</Configuration>
    <PublishDir>bin\Release\net8.0\publish\osx-x64</PublishDir>
    <RuntimeIdentifier>osx-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
  </PropertyGroup>
</Project>
```

### 运行时平台检测
```csharp
// 运行时平台检测
public class PlatformDetector
{
    public static bool IsWindows()
    {
        var platform = Environment.OSVersion.Platform;
        return platform == OperatingSystemType.Windows || 
               platform == OperatingSystemType.Win32NT;
    }

    public static bool IsLinux()
    {
        var platform = Environment.OSVersion.Platform;
        return platform == OperatingSystemType.Linux || 
               (int)platform == 128; // Mono's way of indicating Unix
    }

    public static bool IsMacOS()
    {
        var platform = Environment.OSVersion.Platform;
        return platform == OperatingSystemType.MacOSX;
    }

    public static void ApplyPlatformSpecificSettings()
    {
        if (IsWindows())
        {
            Console.WriteLine("Running on Windows");
        }
        else if (IsLinux())
        {
            Console.WriteLine("Running on Linux");
        }
        else if (IsMacOS())
        {
            Console.WriteLine("Running on macOS");
        }
        else
        {
            Console.WriteLine("Running on unknown platform");
        }
    }
}

// 使用示例
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        PlatformDetector.ApplyPlatformSpecificSettings();
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件