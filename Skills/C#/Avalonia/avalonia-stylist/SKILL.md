---
name: avalonia-stylist
description: "C# Avalonia样式师技能：负责Styles.axaml主题设计、资源字典管理、属性系统使用、样式选择器、平台特定样式。使用时需要设计Styles.axaml主题、管理资源字典、使用属性系统、配置样式选择器、实现平台特定样式。"
---

# avalonia-stylist Skill

为C# Avalonia项目提供专业的样式设计和配置能力，确保应用具有美观、一致、跨平台的视觉效果。

## When to Use This Skill

Trigger when any of these applies:
- 需要设计Styles.axaml主题
- 需要管理资源字典
- 需要使用属性系统
- 需要配置样式选择器
- 需要实现平台特定样式
- 需要优化现有样式实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行样式设计决策
- 不处理样式的深度性能优化（仅设计层面）
- 不负责样式的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** Styles.axaml主题设计
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
        <Setter Property="BorderThickness" Value="0"/>
        <Setter Property="CornerRadius" Value="3"/>
    </Style>

    <!-- 悬停状态 -->
    <Style Selector="Button:hover">
        <Setter Property="Background" Value="#0063B1"/>
    </Style>

    <!-- 按下状态 -->
    <Style Selector="Button:pressed">
        <Setter Property="Background" Value="#004E8C"/>
    </Style>

    <!-- 禁用状态 -->
    <Style Selector="Button:disabled">
        <Setter Property="Background" Value="#E0E0E0"/>
        <Setter Property="Foreground" Value="#808080"/>
    </Style>

    <!-- 文本框样式 -->
    <Style Selector="TextBox">
        <Setter Property="Background" Value="White"/>
        <Setter Property="Foreground" Value="#333333"/>
        <Setter Property="BorderBrush" Value="#CCCCCC"/>
        <Setter Property="BorderThickness" Value="1"/>
        <Setter Property="Padding" Value="8"/>
        <Setter Property="Margin" Value="5"/>
    </Style>

    <!-- 获得焦点状态 -->
    <Style Selector="TextBox:focus">
        <Setter Property="BorderBrush" Value="#0078D7"/>
        <Setter Property="BorderThickness" Value="2"/>
    </Style>
</Styles>
```

**Pattern 2:** 资源字典管理
```xml
<!-- 资源字典定义 -->
<ResourceDictionary xmlns="https://github.com/avaloniaui"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <!-- 颜色资源 -->
    <Color x:Key="PrimaryColor">#0078D7</Color>
    <Color x:Key="SecondaryColor">#0063B1</Color>
    <Color x:Key="AccentColor">#FF6B35</Color>
    <Color x:Key="TextColor">#333333</Color>
    <Color x:Key="BackgroundColor">#F0F0F0</Color>
    <Color x:Key="DisabledColor">#E0E0E0</Color>

    <!-- 画笔资源 -->
    <SolidColorBrush x:Key="PrimaryBrush" Color="{DynamicResource PrimaryColor}"/>
    <SolidColorBrush x:Key="SecondaryBrush" Color="{DynamicResource SecondaryColor}"/>
    <SolidColorBrush x:Key="AccentBrush" Color="{DynamicResource AccentColor}"/>
    <SolidColorBrush x:Key="TextBrush" Color="{DynamicResource TextColor}"/>
    <SolidColorBrush x:Key="BackgroundBrush" Color="{DynamicResource BackgroundColor}"/>
    <SolidColorBrush x:Key="DisabledBrush" Color="{DynamicResource DisabledColor}"/>

    <!-- 尺寸资源 -->
    <Thickness x:Key="DefaultPadding">8</Thickness>
    <Thickness x:Key="DefaultMargin">5</Thickness>
    <CornerRadius x:Key="DefaultCornerRadius">3</CornerRadius>
    <double x:Key="DefaultFontSize">14</double>
    <double x:Key="HeaderFontSize">18</double>
</ResourceDictionary>

<!-- 在App.axaml中引用 -->
<Application xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="MyApp.App">
    <Application.Styles>
        <FluentTheme Mode="Light"/>
        <StyleInclude Source="Styles/ThemeColors.axaml"/>
        <StyleInclude Source="Styles/ControlStyles.axaml"/>
    </Application.Styles>
</Application>
```

**Pattern 3:** 属性系统使用
```csharp
// 自定义控件属性
public class CustomButton : Button
{
    // 注册依赖属性
    public static readonly StyledProperty<Brush> AccentBrushProperty =
        AvaloniaProperty.Register<CustomButton, Brush>(nameof(AccentBrush), Brushes.Blue);

    public static readonly StyledProperty<bool> IsSpecialProperty = 
        AvaloniaProperty.Register<CustomButton, bool>(nameof(IsSpecial), false);

    // CLR属性包装器
    public Brush AccentBrush
    {
        get => GetValue(AccentBrushProperty);
        set => SetValue(AccentBrushProperty, value);
    }

    public bool IsSpecial
    {
        get => GetValue(IsSpecialProperty);
        set => SetValue(IsSpecialProperty, value);
    }

    static CustomButton()
    {
        // 覆盖默认样式
        DefaultStyleKeyProperty.OverrideMetadata(typeof(CustomButton), 
            new FrameworkPropertyMetadata(typeof(CustomButton)));
    }
}

// 在样式中使用
<Style Selector="local|CustomButton">
    <Setter Property="Background" Value="{TemplateBinding AccentBrush}"/>
</Style>

<Style Selector="local|CustomButton[IsSpecial=true]">
    <Setter Property="Background" Value="{DynamicResource AccentBrush}"/>
    <Setter Property="Foreground" Value="White"/>
    <Setter Property="FontWeight" Value="Bold"/>
</Style>
```

**Pattern 4:** 样式选择器
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
        <Setter Property="Foreground" Value="White"/>
    </Style>

    <!-- ID选择器 -->
    <Style Selector="#SubmitButton">
        <Setter Property="Width" Value="120"/>
        <Setter Property="Height" Value="36"/>
    </Style>

    <!-- 属性选择器 -->
    <Style Selector="Button[IsDefault=true]">
        <Setter Property="FontWeight" Value="Bold"/>
    </Style>

    <!-- 组合选择器 -->
    <Style Selector="StackPanel > Button">
        <Setter Property="Margin" Value="5,0,5,0"/>
    </Style>

    <!-- 多条件选择器 -->
    <Style Selector="Button:pointerover:enabled">
        <Setter Property="Background" Value="#0063B1"/>
    </Style>

    <!-- 伪类选择器 -->
    <Style Selector="TextBox:focus">
        <Setter Property="BorderBrush" Value="#0078D7"/>
    </Style>

    <Style Selector="ListBoxItem:selected">
        <Setter Property="Background" Value="#E6F7FF"/>
        <Setter Property="Foreground" Value="#0078D7"/>
    </Style>
</Styles>
```

**Pattern 5:** 平台特定样式
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

    <!-- Linux特定样式 -->
    <Style Selector="Window[@os='Linux']">
        <Setter Property="FontFamily" Value="Ubuntu"/>
    </Style>
</Styles>

// 在代码中设置平台特定属性
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        SetPlatformSpecificProperties();
    }

    private void SetPlatformSpecificProperties()
    {
        var platform = AvaloniaLocator.Current.GetService<IRuntimePlatform>().GetRuntimeInfo().OperatingSystem;
        
        switch (platform)
        {
            case OperatingSystemType.Windows:
                this.Resources["WindowFontFamily"] = new FontFamily("Segoe UI");
                break;
            case OperatingSystemType.MacOS:
                this.Resources["WindowFontFamily"] = new FontFamily(".SF NS Text");
                break;
            case OperatingSystemType.Linux:
                this.Resources["WindowFontFamily"] = new FontFamily("Ubuntu");
                break;
        }
    }
}
```

## Examples

### Example 1: 设计Styles.axaml主题
- Input: 需要为应用设计主题样式
- Steps:
  1. 创建Styles.axaml文件
  2. 定义基本控件样式
  3. 添加状态样式（hover、pressed等）
  4. 在App.axaml中引用
  5. 测试样式效果
- Expected output / acceptance: 主题样式正确应用和显示

### Example 2: 管理资源字典
- Input: 需要管理应用的颜色和尺寸资源
- Steps:
  1. 创建资源字典文件
  2. 定义颜色、画笔等资源
  3. 在样式中引用资源
  4. 测试资源使用
- Expected output / acceptance: 资源正确管理和使用

### Example 3: 使用样式选择器
- Input: 需要为不同状态的控件设置样式
- Steps:
  1. 创建样式文件
  2. 使用不同类型的选择器
  3. 测试选择器效果
- Expected output / acceptance: 样式选择器正确应用

## References

- `references/index.md`: Avalonia样式设计最佳实践导航
- `references/styles-axaml.md`: Styles.axaml主题设计指南
- `references/resource-dictionaries.md`: 资源字典管理指南
- `references/property-system.md`: 属性系统使用指南
- `references/style-selectors.md`: 样式选择器配置指南
- `references/platform-specific.md`: 平台特定样式实现指南

## Maintenance

- Sources: Avalonia官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现