---
name: cross-platform-packager
description: "C# Avalonia跨平台打包器技能：负责MSIX包创建、AppImage生成、DMG打包、发布配置管理、运行时平台检测。使用时需要创建MSIX包、生成AppImage、打包DMG、管理发布配置、检测运行时平台。"
---

# cross-platform-packager Skill

为C# Avalonia项目提供专业的跨平台打包和发布能力，确保应用能够在不同平台上正确打包和分发。

## When to Use This Skill

Trigger when any of these applies:
- 需要创建MSIX包
- 需要生成AppImage
- 需要打包DMG
- 需要管理发布配置
- 需要检测运行时平台
- 需要优化现有打包实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行打包设计决策
- 不处理打包的深度性能优化（仅设计层面）
- 不负责打包的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** MSIX包创建
```xml
<!-- MSIX打包配置 -->
<!-- 在项目文件(.csproj)中添加 -->
<PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows</TargetFramework>
    <Nullable>enable</Nullable>
    <UseWPF>false</UseWPF>
    <UseWindowsForms>false</UseWindowsForms>
    <PublishProfile>win10-x64</PublishProfile>
    <RuntimeIdentifier>win10-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
    <PublishReadyToRun>true</PublishReadyToRun>
    <PublishTrimmed>true</PublishTrimmed>
    <GenerateAppxPackageOnBuild>true</GenerateAppxPackageOnBuild>
    <AppxPackageSigningEnabled>true</AppxPackageSigningEnabled>
    <PackageCertificateThumbprint>YOUR_CERTIFICATE_THUMBPRINT</PackageCertificateThumbprint>
    <PackageCertificatePassword>YOUR_CERTIFICATE_PASSWORD</PackageCertificatePassword>
</PropertyGroup>

<!-- 使用Avalonia MSIX打包工具 -->
<ItemGroup>
    <PackageReference Include="Avalonia.Desktop" Version="11.0.0" />
    <PackageReference Include="Avalonia.MSIX" Version="11.0.0" />
</ItemGroup>

<!-- AppxManifest.xml配置 -->
<ItemGroup>
    <None Update="AppxManifest.xml">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
</ItemGroup>
```

```xml
<!-- AppxManifest.xml示例 -->
<?xml version="1.0" encoding="utf-8"?>
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
  IgnorableNamespaces="uap rescap">

  <Identity
    Name="MyApp"
    Publisher="CN=My Company"
    Version="1.0.0.0"
    ProcessorArchitecture="x64" />

  <Properties>
    <DisplayName>My Application</DisplayName>
    <PublisherDisplayName>My Company</PublisherDisplayName>
    <Logo>Assets\StoreLogo.png</Logo>
  </Properties>

  <Resources>
    <Resource Language="en-us" />
  </Resources>

  <Dependencies>
    <TargetDeviceFamily Name="Windows.Universal" MinVersion="10.0.17763.0" MaxVersionTested="10.0.19041.0" />
    <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.17763.0" MaxVersionTested="10.0.19041.0" />
  </Dependencies>

  <Capabilities>
    <rescap:Capability Name="runFullTrust" />
  </Capabilities>

  <Applications>
    <Application Id="MyApp" Executable="MyApp.exe" EntryPoint="Windows.FullTrustApplication">
      <uap:VisualElements
        DisplayName="My Application"
        Description="My Application Description"
        BackgroundColor="#464646"
        Square150x150Logo="Assets\Square150x150Logo.png"
        Square44x44Logo="Assets\Square44x44Logo.png">
        <uap:SplashScreen Image="Assets\SplashScreen.png" />
      </uap:VisualElements>
    </Application>
  </Applications>
</Package>
```

**Pattern 2:** AppImage生成
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
mkdir -p "$APP_DIR/usr/lib"
mkdir -p "$APP_DIR/usr/share/applications"
mkdir -p "$APP_DIR/usr/share/icons/hicolor/256x256/apps"

# 复制应用文件
dotnet publish -c Release -r linux-x64 --self-contained true -p:PublishSingleFile=true -p:PublishReadyToRun=true
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

# 复制图标
cp Assets/icon.png "$APP_DIR/usr/share/icons/hicolor/256x256/apps/$APP_NAME.png"
ln -s "usr/share/icons/hicolor/256x256/apps/$APP_NAME.png" "$APP_DIR/$APP_NAME.png"

# 创建启动脚本
cat > "$APP_DIR/AppRun" << EOF
#!/bin/sh
cd "\$(dirname "\$0")"
exec ./usr/bin/$APP_NAME "\$@"
EOF
chmod +x "$APP_DIR/AppRun"

# 下载AppImageTool
wget -O "$OUTPUT_DIR/appimagetool" "https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-x86_64.AppImage"
chmod +x "$OUTPUT_DIR/appimagetool"

# 生成AppImage
"$OUTPUT_DIR/appimagetool" "$APP_DIR" "$OUTPUT_DIR/$APP_NAME-$APP_VERSION-x86_64.AppImage"

# 清理
rm -rf "$APP_DIR"
rm "$OUTPUT_DIR/appimagetool"

echo "AppImage created successfully: $OUTPUT_DIR/$APP_NAME-$APP_VERSION-x86_64.AppImage"
```

**Pattern 3:** DMG打包
```bash
# DMG打包脚本
#!/bin/bash

# 设置变量
APP_NAME="MyApp"
APP_VERSION="1.0.0"
OUTPUT_DIR="output"
DMG_NAME="$APP_NAME-$APP_VERSION.dmg"
DMG_TEMP="$OUTPUT_DIR/temp"

# 创建目录
mkdir -p "$DMG_TEMP"

# 构建应用
dotnet publish -c Release -r osx-x64 --self-contained true -p:PublishSingleFile=true -p:PublishReadyToRun=true

# 复制应用到临时目录
cp -r bin/Release/net8.0/osx-x64/publish/$APP_NAME.app "$DMG_TEMP/"

# 创建背景目录
mkdir -p "$DMG_TEMP/.background"
cp Assets/dmg_background.png "$DMG_TEMP/.background/"

# 创建DMG
hdiutil create -srcfolder "$DMG_TEMP" -volname "$APP_NAME" -fs HFS+ -fsargs "-c c=64,a=16,e=16" -format UDRW -size 100m "$OUTPUT_DIR/temp.dmg"

# 挂载DMG
hdiutil attach "$OUTPUT_DIR/temp.dmg" -noautoopen -mountpoint "/Volumes/$APP_NAME"

# 设置背景
osascript << EOF
tell application "Finder"
    tell disk "$APP_NAME"
        open
        set current view of container window to icon view
        set toolbar visible of container window to false
        set statusbar visible of container window to false
        set the bounds of container window to {400, 100, 880, 430}
        set theViewOptions to the icon view options of container window
        set arrangement of theViewOptions to not arranged
        set icon size of theViewOptions to 72
        set background picture of theViewOptions to file ".background:dmg_background.png"
        make new alias file at container window to POSIX file "/Applications" with properties {name:"Applications"}
        set position of item "$APP_NAME.app" of container window to {100, 100}
        set position of item "Applications" of container window to {300, 100}
        close
        open
        update without registering applications
        delay 2
        close
    end tell
end tell
EOF

# 卸载并转换为只读DMG
hdiutil detach "/Volumes/$APP_NAME"
hdiutil convert "$OUTPUT_DIR/temp.dmg" -format UDZO -o "$OUTPUT_DIR/$DMG_NAME"

# 清理
rm "$OUTPUT_DIR/temp.dmg"
rm -rf "$DMG_TEMP"

echo "DMG created successfully: $OUTPUT_DIR/$DMG_NAME"
```

**Pattern 4:** 发布配置管理
```xml
<!-- 发布配置文件 -->
<!-- win10-x64.pubxml -->
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration>Release</Configuration>
    <Platform>Any CPU</Platform>
    <PublishDir>bin\Release\net8.0-windows\publish\win10-x64</PublishDir>
    <PublishProtocol>FileSystem</PublishProtocol>
    <TargetFramework>net8.0-windows</TargetFramework>
    <RuntimeIdentifier>win10-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
    <PublishReadyToRun>true</PublishReadyToRun>
    <PublishTrimmed>true</PublishTrimmed>
    <PublishReadyToRunShowWarnings>false</PublishReadyToRunShowWarnings>
  </PropertyGroup>
</Project>

<!-- linux-x64.pubxml -->
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration>Release</Configuration>
    <Platform>Any CPU</Platform>
    <PublishDir>bin\Release\net8.0\publish\linux-x64</PublishDir>
    <PublishProtocol>FileSystem</PublishProtocol>
    <TargetFramework>net8.0</TargetFramework>
    <RuntimeIdentifier>linux-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
    <PublishReadyToRun>true</PublishReadyToRun>
    <PublishTrimmed>true</PublishTrimmed>
  </PropertyGroup>
</Project>

<!-- osx-x64.pubxml -->
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration>Release</Configuration>
    <Platform>Any CPU</Platform>
    <PublishDir>bin\Release\net8.0\publish\osx-x64</PublishDir>
    <PublishProtocol>FileSystem</PublishProtocol>
    <TargetFramework>net8.0</TargetFramework>
    <RuntimeIdentifier>osx-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
    <PublishReadyToRun>true</PublishReadyToRun>
    <PublishTrimmed>true</PublishTrimmed>
  </PropertyGroup>
</Project>
```

**Pattern 5:** 运行时平台检测
```csharp
// 运行时平台检测
public class PlatformDetector
{
    public static OperatingSystemType GetCurrentPlatform()
    {
        return Environment.OSVersion.Platform;
    }

    public static bool IsWindows()
    {
        var platform = GetCurrentPlatform();
        return platform == OperatingSystemType.Windows || 
               platform == OperatingSystemType.Win32NT || 
               platform == OperatingSystemType.Win32S || 
               platform == OperatingSystemType.Win32Windows || 
               platform == OperatingSystemType.WinCE;
    }

    public static bool IsLinux()
    {
        var platform = GetCurrentPlatform();
        return platform == OperatingSystemType.Linux || 
               (int)platform == 128; // Mono's way of indicating Unix
    }

    public static bool IsMacOS()
    {
        var platform = GetCurrentPlatform();
        return platform == OperatingSystemType.MacOSX;
    }

    public static string GetPlatformSpecificPath(string relativePath)
    {
        if (IsWindows())
        {
            return Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), relativePath);
        }
        else if (IsLinux())
        {
            return Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.UserProfile), ".local", "share", relativePath);
        }
        else if (IsMacOS())
        {
            return Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.UserProfile), "Library", "Application Support", relativePath);
        }
        else
        {
            throw new PlatformNotSupportedException("Unsupported platform");
        }
    }

    public static void ApplyPlatformSpecificSettings()
    {
        if (IsWindows())
        {
            // Windows specific settings
            Console.WriteLine("Running on Windows");
        }
        else if (IsLinux())
        {
            // Linux specific settings
            Console.WriteLine("Running on Linux");
        }
        else if (IsMacOS())
        {
            // macOS specific settings
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
        LoadPlatformSpecificResources();
    }

    private void LoadPlatformSpecificResources()
    {
        if (PlatformDetector.IsWindows())
        {
            // 加载Windows特定资源
        }
        else if (PlatformDetector.IsLinux())
        {
            // 加载Linux特定资源
        }
        else if (PlatformDetector.IsMacOS())
        {
            // 加载macOS特定资源
        }
    }
}
```

## Examples

### Example 1: 创建MSIX包
- Input: 需要为Windows平台创建MSIX包
- Steps:
  1. 配置项目文件
  2. 创建AppxManifest.xml
  3. 生成MSIX包
  4. 测试MSIX包安装
- Expected output / acceptance: MSIX包正确创建和安装

### Example 2: 生成AppImage
- Input: 需要为Linux平台生成AppImage
- Steps:
  1. 创建AppImage生成脚本
  2. 构建Linux版本应用
  3. 运行脚本生成AppImage
  4. 测试AppImage运行
- Expected output / acceptance: AppImage正确生成和运行

### Example 3: 打包DMG
- Input: 需要为macOS平台打包DMG
- Steps:
  1. 创建DMG打包脚本
  2. 构建macOS版本应用
  3. 运行脚本生成DMG
  4. 测试DMG安装
- Expected output / acceptance: DMG正确生成和安装

## References

- `references/index.md`: 跨平台打包最佳实践导航
- `references/msix-packaging.md`: MSIX包创建指南
- `references/appimage-generation.md`: AppImage生成指南
- `references/dmg-packaging.md`: DMG打包指南
- `references/publish-configuration.md`: 发布配置管理指南
- `references/platform-detection.md`: 运行时平台检测指南

## Maintenance

- Sources: Avalonia官方文档和跨平台打包最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现