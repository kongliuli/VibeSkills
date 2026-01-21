# Avalonia样式师 (avalonia-stylist)

为C# Avalonia项目提供专业的样式设计和配置能力，确保应用具有美观、一致、跨平台的视觉效果。

## 主要功能

- **Styles.axaml主题设计**：设计和配置Avalonia主题样式
- **资源字典管理**：管理和组织样式资源字典
- **属性系统使用**：使用和扩展Avalonia属性系统
- **样式选择器**：配置和使用各种样式选择器
- **平台特定样式**：实现和管理平台特定样式

## 使用场景

- 需要设计Styles.axaml主题
- 需要管理资源字典
- 需要使用属性系统
- 需要配置样式选择器
- 需要实现平台特定样式
- 需要优化现有样式实现

## 快速开始

### Styles.axaml主题设计
```xml
<!-- Styles.axaml主题设计 -->
<Styles xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <!-- 基础样式 -->
    <Style Selector="Window">
        <Setter Property="Background" Value="#F0F0F0"/>
        <Setter Property="FontFamily" Value="Segoe UI, Arial, sans-serif"/>
        <Setter Property="FontSize" Value="14"/>
    </Style>

    <!-- 按钮样式 -->
    <Style Selector="Button">
        <Setter Property="Background" Value="#0078D7"/>
        <Setter Property="Foreground" Value="White"/>
        <Setter Property="Padding" Value="10,5"/>
        <Setter Property="Margin" Value="5"/>
    </Style>

    <!-- 悬停状态 -->
    <Style Selector="Button:hover">
        <Setter Property="Background" Value="#0063B1"/>
    </Style>
</Styles>
```

### 资源字典管理
```xml
<!-- 资源字典定义 -->
<ResourceDictionary xmlns="https://github.com/avaloniaui"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <!-- 颜色资源 -->
    <Color x:Key="PrimaryColor">#0078D7</Color>
    <Color x:Key="SecondaryColor">#0063B1</Color>
    <Color x:Key="AccentColor">#FF6B35</Color>

    <!-- 画笔资源 -->
    <SolidColorBrush x:Key="PrimaryBrush" Color="{DynamicResource PrimaryColor}"/>
    <SolidColorBrush x:Key="SecondaryBrush" Color="{DynamicResource SecondaryColor}"/>
    <SolidColorBrush x:Key="AccentBrush" Color="{DynamicResource AccentColor}"/>
</ResourceDictionary>

<!-- 在App.axaml中引用 -->
<Application.Styles>
    <FluentTheme Mode="Light"/>
    <StyleInclude Source="Styles/ThemeColors.axaml"/>
    <StyleInclude Source="Styles/ControlStyles.axaml"/>
</Application.Styles>
```

### 属性系统使用
```csharp
// 自定义控件属性
public class CustomButton : Button
{
    // 注册依赖属性
    public static readonly StyledProperty<Brush> AccentBrushProperty =
        AvaloniaProperty.Register<CustomButton, Brush>(nameof(AccentBrush), Brushes.Blue);

    // CLR属性包装器
    public Brush AccentBrush
    {
        get => GetValue(AccentBrushProperty);
        set => SetValue(AccentBrushProperty, value);
    }

    static CustomButton()
    {
        DefaultStyleKeyProperty.OverrideMetadata(typeof(CustomButton), 
            new FrameworkPropertyMetadata(typeof(CustomButton)));
    }
}
```

### 样式选择器
```xml
<!-- 样式选择器 -->
<Styles xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <!-- 类型选择器 -->
    <Style Selector="Button">
        <Setter Property="Background" Value="#0078D7"/>
    </Style>

    <!-- 类选择器 -->
    <Style Selector=".PrimaryButton">
        <Setter Property="Background" Value="#0078D7"/>
    </Style>

    <!-- 属性选择器 -->
    <Style Selector="Button[IsDefault=true]">
        <Setter Property="FontWeight" Value="Bold"/>
    </Style>

    <!-- 伪类选择器 -->
    <Style Selector="Button:hover">
        <Setter Property="Background" Value="#0063B1"/>
    </Style>
</Styles>
```

### 平台特定样式
```xml
<!-- 平台特定样式 -->
<Styles xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <!-- 通用样式 -->
    <Style Selector="Window">
        <Setter Property="Background" Value="#F0F0F0"/>
    </Style>

    <!-- Windows特定样式 -->
    <Style Selector="Window[@os='Windows']">
        <Setter Property="FontFamily" Value="Segoe UI"/>
    </Style>

    <!-- macOS特定样式 -->
    <Style Selector="Window[@os='MacOS']">
        <Setter Property="FontFamily" Value=".SF NS Text"/>
    </Style>
</Styles>
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件