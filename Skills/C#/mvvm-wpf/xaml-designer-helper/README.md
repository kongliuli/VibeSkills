# XAML设计师助手 (xaml-designer-helper)

为C# MVVM WPF项目提供专业的XAML设计和配置能力，确保UI具有美观、一致、可维护的实现。

## 主要功能

- **数据模板设计**：设计和配置数据模板
- **控件模板定制**：定制和修改控件模板
- **样式资源管理**：管理和组织样式资源
- **触发器配置**：配置和使用各种触发器
- **值转换器实现**：实现和使用值转换器

## 使用场景

- 需要设计数据模板
- 需要定制控件模板
- 需要管理样式资源
- 需要配置触发器
- 需要实现值转换器(IValueConverter)
- 需要优化现有XAML实现

## 快速开始

### 数据模板设计
```xml
<!-- 数据模板设计 -->
<DataTemplate x:Key="UserItemTemplate">
    <StackPanel Orientation="Horizontal" Margin="5">
        <Ellipse Width="20" Height="20" Fill="{Binding Status, Converter={StaticResource StatusToColorConverter}}">
            <Ellipse.ToolTip>
                <TextBlock Text="{Binding Status}"/>
            </Ellipse.ToolTip>
        </Ellipse>
        <TextBlock Text="{Binding Name}" Margin="10,0,0,0" FontWeight="Bold"/>
        <TextBlock Text="{Binding Email}" Margin="10,0,0,0" Foreground="Gray"/>
    </StackPanel>
</DataTemplate>

<!-- 使用数据模板 -->
<ListBox ItemsSource="{Binding Users}" ItemTemplate="{StaticResource UserItemTemplate}"/>
```

### 控件模板定制
```xml
<!-- 控件模板定制 -->
<ControlTemplate x:Key="CustomButtonTemplate" TargetType="Button">
    <Border x:Name="Border" 
            Background="{TemplateBinding Background}" 
            BorderBrush="{TemplateBinding BorderBrush}" 
            BorderThickness="{TemplateBinding BorderThickness}"
            CornerRadius="4">
        <ContentPresenter x:Name="ContentPresenter" 
                          Content="{TemplateBinding Content}" 
                          ContentTemplate="{TemplateBinding ContentTemplate}"
                          HorizontalAlignment="{TemplateBinding HorizontalContentAlignment}"
                          VerticalAlignment="{TemplateBinding VerticalContentAlignment}"
                          Margin="{TemplateBinding Padding}"/>
    </Border>
    <ControlTemplate.Triggers>
        <Trigger Property="IsMouseOver" Value="True">
            <Setter TargetName="Border" Property="Background" Value="#E0E0E0"/>
        </Trigger>
        <Trigger Property="IsPressed" Value="True">
            <Setter TargetName="Border" Property="Background" Value="#C0C0C0"/>
            <Setter TargetName="ContentPresenter" Property="Margin" Value="6,6,4,4"/>
        </Trigger>
    </ControlTemplate.Triggers>
</ControlTemplate>
```

### 样式资源管理
```xml
<!-- 样式资源管理 -->
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    
    <!-- 颜色资源 -->
    <Color x:Key="PrimaryColor">#0066CC</Color>
    <Color x:Key="SecondaryColor">#0099FF</Color>
    
    <!-- 画笔资源 -->
    <SolidColorBrush x:Key="PrimaryBrush" Color="{StaticResource PrimaryColor}"/>
    <SolidColorBrush x:Key="SecondaryBrush" Color="{StaticResource SecondaryColor}"/>
    
    <!-- 按钮样式 -->
    <Style x:Key="PrimaryButtonStyle" TargetType="Button">
        <Setter Property="Background" Value="{StaticResource PrimaryBrush}"/>
        <Setter Property="Foreground" Value="White"/>
        <Setter Property="Padding" Value="10,5"/>
    </Style>
    
</ResourceDictionary>
```

### 触发器配置
```xml
<!-- 触发器配置 -->
<Style x:Key="StatusTextBlockStyle" TargetType="TextBlock">
    <Setter Property="FontWeight" Value="Normal"/>
    <Setter Property="Foreground" Value="Gray"/>
    
    <!-- 属性触发器 -->
    <Style.Triggers>
        <Trigger Property="Text" Value="Active">
            <Setter Property="Foreground" Value="Green"/>
            <Setter Property="FontWeight" Value="Bold"/>
        </Trigger>
        <Trigger Property="Text" Value="Inactive">
            <Setter Property="Foreground" Value="Red"/>
        </Trigger>
        
        <!-- 数据触发器 -->
        <DataTrigger Binding="{Binding IsAdmin}" Value="True">
            <Setter Property="FontStyle" Value="Italic"/>
        </DataTrigger>
    </Style.Triggers>
</Style>
```

### 值转换器实现
```csharp
// 值转换器实现
public class StatusToColorConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value is string status)
        {
            switch (status)
            {
                case "Active":
                    return Brushes.Green;
                case "Inactive":
                    return Brushes.Red;
                case "Pending":
                    return Brushes.Orange;
                default:
                    return Brushes.Gray;
            }
        }
        return Brushes.Gray;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        throw new NotImplementedException();
    }
}

<!-- 在XAML中使用 -->
<Window.Resources>
    <local:StatusToColorConverter x:Key="StatusToColorConverter"/>
</Window.Resources>
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件