---
name: winforms-dotnet-migration-assistant
description: "C# WinForms .NET迁移助手技能：负责.NET Framework到.NET 8+迁移、兼容性检查、旧API替换、依赖项升级、现代化改造。使用时需要迁移.NET Framework到.NET 8+、检查兼容性、替换旧API、升级依赖项、进行现代化改造。"
---

# winforms-dotnet-migration-assistant Skill

为C# WinForms项目提供专业的.NET框架迁移和现代化能力，确保应用能够从.NET Framework顺利迁移到.NET 8+，并进行必要的现代化改造。

## When to Use This Skill

Trigger when any of these applies:
- 需要迁移.NET Framework到.NET 8+
- 需要检查兼容性
- 需要替换旧API
- 需要升级依赖项
- 需要进行现代化改造
- 需要优化现有迁移实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行迁移决策
- 不处理迁移的深度性能优化（仅设计层面）
- 不负责迁移的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** .NET Framework到.NET 8+迁移
```xml
<!-- 1. 更新项目文件(.csproj) -->
<!-- 旧的.NET Framework项目文件 -->
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration Condition=" '$(Configuration)' == '' ">Debug</Configuration>
    <Platform Condition=" '$(Platform)' == '' ">AnyCPU</Platform>
    <ProductVersion>8.0.30703</ProductVersion>
    <SchemaVersion>2.0</SchemaVersion>
    <ProjectGuid>{YOUR-GUID-HERE}</ProjectGuid>
    <OutputType>WinExe</OutputType>
    <AppDesignerFolder>Properties</AppDesignerFolder>
    <RootNamespace>MyWinFormsApp</RootNamespace>
    <AssemblyName>MyWinFormsApp</AssemblyName>
    <TargetFrameworkVersion>v4.8</TargetFrameworkVersion>
    <FileAlignment>512</FileAlignment>
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    <TargetFrameworkProfile />
  </PropertyGroup>
  <ItemGroup>
    <Reference Include="System" />
    <Reference Include="System.Core" />
    <Reference Include="System.Data" />
    <Reference Include="System.Drawing" />
    <Reference Include="System.Windows.Forms" />
    <Reference Include="System.Xml" />
  </ItemGroup>
  <ItemGroup>
    <Compile Include="Form1.cs">
      <SubType>Form</SubType>
    </Compile>
    <Compile Include="Form1.Designer.cs">
      <DependentUpon>Form1.cs</DependentUpon>
    </Compile>
    <Compile Include="Program.cs" />
    <Compile Include="Properties\AssemblyInfo.cs" />
  </ItemGroup>
  <ItemGroup>
    <EmbeddedResource Include="Form1.resx">
      <DependentUpon>Form1.cs</DependentUpon>
    </EmbeddedResource>
    <EmbeddedResource Include="Properties\Resources.resx">
      <Generator>ResXFileCodeGenerator</Generator>
      <LastGenOutput>Resources.Designer.cs</LastGenOutput>
      <SubType>Designer</SubType>
    </EmbeddedResource>
    <Compile Include="Properties\Resources.Designer.cs">
      <AutoGen>True</AutoGen>
      <DependentUpon>Resources.resx</DependentUpon>
      <DesignTime>True</DesignTime>
    </Compile>
    <None Include="Properties\Settings.settings">
      <Generator>SettingsSingleFileGenerator</Generator>
      <LastGenOutput>Settings.Designer.cs</LastGenOutput>
    </None>
    <Compile Include="Properties\Settings.Designer.cs">
      <AutoGen>True</AutoGen>
      <DependentUpon>Settings.settings</DependentUpon>
      <DesignTimeSharedInput>True</DesignTimeSharedInput>
    </Compile>
  </ItemGroup>
</Project>

<!-- 新的.NET 8项目文件 -->
<Project Sdk="Microsoft.NET.Sdk.WindowsDesktop">

  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows</TargetFramework>
    <Nullable>enable</Nullable>
    <UseWindowsForms>true</UseWindowsForms>
    <ImplicitUsings>enable</ImplicitUsings>
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
  </PropertyGroup>

  <ItemGroup>
    <None Remove="Properties\Settings.settings" />
    <Content Include="Properties\Settings.settings" />
  </ItemGroup>

  <ItemGroup>
    <Compile Update="Properties\Settings.Designer.cs">
      <DesignTimeSharedInput>True</DesignTimeSharedInput>
    </Compile>
  </ItemGroup>

</Project>

<!-- 2. 更新Program.cs -->
// 旧的Program.cs
static class Program
{
    /// <summary>
    /// 应用程序的主入口点。
    /// </summary>
    [STAThread]
    static void Main()
    {
        Application.EnableVisualStyles();
        Application.SetCompatibleTextRenderingDefault(false);
        Application.Run(new Form1());
    }
}

// 新的Program.cs
namespace MyWinFormsApp
{
    internal static class Program
    {
        /// <summary>
        /// 应用程序的主入口点。
        /// </summary>
        [STAThread]
        static void Main()
        {
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);
            Application.Run(new Form1());
        }
    }
}

<!-- 3. 更新AssemblyInfo.cs (如果需要) -->
// 旧的AssemblyInfo.cs
using System.Reflection;
using System.Runtime.CompilerServices;
using System.Runtime.InteropServices;

// 有关程序集的一般信息由以下
// 控制。更改这些特性值可修改
// 与程序集关联的信息。
[assembly: AssemblyTitle("MyWinFormsApp")]
[assembly: AssemblyDescription("")]
[assembly: AssemblyConfiguration("")]
[assembly: AssemblyCompany("")]
[assembly: AssemblyProduct("MyWinFormsApp")]
[assembly: AssemblyCopyright("Copyright ©  2024")]
[assembly: AssemblyTrademark("")]
[assembly: AssemblyCulture("")]

// 将 ComVisible 设置为 false 会使此程序集中的类型
//对 COM 组件不可见。  如果需要从 COM 访问此程序集中的类型，
//请将此类型的 ComVisible 特性设置为 true。
[assembly: ComVisible(false)]

// 如果此项目向 COM 公开，则下列 GUID 用于类型库的 ID
[assembly: Guid("YOUR-GUID-HERE")]

// 程序集的版本信息由下列四个值组成：
//
//      主版本
//      次版本
//      生成号
//      修订号
//
// 可以指定所有这些值，也可以使用“生成号”和“修订号”的默认值，
// 方法是按如下所示使用 "*"：
// [assembly: AssemblyVersion("1.0.*")]
[assembly: AssemblyVersion("1.0.0.0")]
[assembly: AssemblyFileVersion("1.0.0.0")]

// 新的AssemblyInfo.cs (可选，因为大部分信息现在在项目文件中)
// 只保留需要的特性
using System.Reflection;
using System.Runtime.InteropServices;

[assembly: ComVisible(false)]
[assembly: Guid("YOUR-GUID-HERE")]
```

**Pattern 2:** 兼容性检查
```csharp
// 兼容性检查工具类
public static class CompatibilityChecker
{
    // 检查.NET版本兼容性
    public static void CheckDotNetVersionCompatibility()
    {
        Console.WriteLine("=== .NET版本兼容性检查 ===");
        
        // 检查当前运行时版本
        var runtimeVersion = System.Runtime.InteropServices.RuntimeInformation.FrameworkDescription;
        Console.WriteLine($"当前运行时: {runtimeVersion}");
        
        // 检查操作系统
        var osDescription = System.Runtime.InteropServices.RuntimeInformation.OSDescription;
        var osArchitecture = System.Runtime.InteropServices.RuntimeInformation.OSArchitecture;
        Console.WriteLine($"操作系统: {osDescription} ({osArchitecture})");
        
        // 检查处理器架构
        var processArchitecture = System.Runtime.InteropServices.RuntimeInformation.ProcessArchitecture;
        Console.WriteLine($"处理器架构: {processArchitecture}");
        
        Console.WriteLine("=== 检查完成 ===");
    }

    // 检查API兼容性
    public static List<string> CheckApiCompatibility()
    {
        var issues = new List<string>();
        
        Console.WriteLine("=== API兼容性检查 ===");
        
        // 检查过时API的使用
        try
        {
            // 示例：检查是否使用了过时的方法
            // 实际项目中，这可能需要更复杂的静态分析
            Console.WriteLine("检查过时API使用...");
            // 这里可以添加具体的API检查逻辑
        }
        catch (Exception ex)
        {
            issues.Add($"API检查错误: {ex.Message}");
        }
        
        // 检查Windows特定API
        try
        {
            Console.WriteLine("检查Windows特定API...");
            // 检查是否使用了Windows特定的API
        }
        catch (Exception ex)
        {
            issues.Add($"Windows API检查错误: {ex.Message}");
        }
        
        Console.WriteLine("=== API兼容性检查完成 ===");
        return issues;
    }

    // 检查依赖项兼容性
    public static List<string> CheckDependencyCompatibility()
    {
        var issues = new List<string>();
        
        Console.WriteLine("=== 依赖项兼容性检查 ===");
        
        try
        {
            // 检查项目文件中的依赖项
            var projectFile = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "..", "..", "MyWinFormsApp.csproj");
            if (File.Exists(projectFile))
            {
                var projectContent = File.ReadAllText(projectFile);
                Console.WriteLine("检查项目依赖项...");
                
                // 检查是否有不兼容的包引用
                if (projectContent.Contains("PackageReference"))
                {
                    // 这里可以添加具体的依赖项检查逻辑
                }
            }
            else
            {
                issues.Add("找不到项目文件");
            }
        }
        catch (Exception ex)
        {
            issues.Add($"依赖项检查错误: {ex.Message}");
        }
        
        Console.WriteLine("=== 依赖项兼容性检查完成 ===");
        return issues;
    }

    // 生成兼容性报告
    public static void GenerateCompatibilityReport(string outputPath)
    {
        Console.WriteLine("=== 生成兼容性报告 ===");
        
        var reportContent = new StringBuilder();
        reportContent.AppendLine("# .NET迁移兼容性报告");
        reportContent.AppendLine($"生成时间: {DateTime.Now}");
        reportContent.AppendLine();
        
        // 添加版本信息
        reportContent.AppendLine("## 版本信息");
        reportContent.AppendLine($"当前运行时: {System.Runtime.InteropServices.RuntimeInformation.FrameworkDescription}");
        reportContent.AppendLine($"操作系统: {System.Runtime.InteropServices.RuntimeInformation.OSDescription}");
        reportContent.AppendLine();
        
        // 添加API兼容性问题
        var apiIssues = CheckApiCompatibility();
        reportContent.AppendLine("## API兼容性问题");
        if (apiIssues.Count > 0)
        {
            foreach (var issue in apiIssues)
            {
                reportContent.AppendLine($"- {issue}");
            }
        }
        else
        {
            reportContent.AppendLine("- 无API兼容性问题");
        }
        reportContent.AppendLine();
        
        // 添加依赖项兼容性问题
        var dependencyIssues = CheckDependencyCompatibility();
        reportContent.AppendLine("## 依赖项兼容性问题");
        if (dependencyIssues.Count > 0)
        {
            foreach (var issue in dependencyIssues)
            {
                reportContent.AppendLine($"- {issue}");
            }
        }
        else
        {
            reportContent.AppendLine("- 无依赖项兼容性问题");
        }
        
        // 保存报告
        File.WriteAllText(outputPath, reportContent.ToString());
        Console.WriteLine($"报告已生成: {outputPath}");
    }
}
```

**Pattern 3:** 旧API替换
```csharp
// 旧API替换示例

// 1. 替换过时的Windows Forms API
// 旧代码
private void OldMethod()
{
    // 使用过时的方法
    this.SuspendLayout();
    // 添加控件
    this.ResumeLayout(false);
    this.PerformLayout();
}

// 新代码
private void NewMethod()
{
    // 现代写法，功能相同
    this.SuspendLayout();
    // 添加控件
    this.ResumeLayout(false);
    this.PerformLayout();
}

// 2. 替换过时的集合类
// 旧代码
private void OldCollectionUsage()
{
    ArrayList list = new ArrayList();
    list.Add("item1");
    list.Add("item2");
    
    foreach (object item in list)
    {
        Console.WriteLine(item.ToString());
    }
}

// 新代码
private void NewCollectionUsage()
{
    List<string> list = new List<string>();
    list.Add("item1");
    list.Add("item2");
    
    foreach (string item in list)
    {
        Console.WriteLine(item);
    }
}

// 3. 替换过时的委托语法
// 旧代码
private void OldDelegateUsage()
{
    button1.Click += new EventHandler(button1_Click);
}

private void button1_Click(object sender, EventArgs e)
{
    // 处理点击事件
}

// 新代码
private void NewDelegateUsage()
{
    button1.Click += button1_Click;
    // 或者使用lambda表达式
    button1.Click += (sender, e) =>
    {
        // 处理点击事件
    };
}

// 4. 替换过时的异常处理
// 旧代码
private void OldExceptionHandling()
{
    try
    {
        // 可能抛出异常的代码
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        // 清理代码
    }
}

// 新代码
private void NewExceptionHandling()
{
    try
    {
        // 可能抛出异常的代码
    }
    catch (Exception ex) when (ex is ArgumentException || ex is InvalidOperationException)
    {
        Console.WriteLine(ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine($"未处理的异常: {ex.Message}");
    }
    finally
    {
        // 清理代码
    }
}

// 5. 替换过时的文件操作
// 旧代码
private void OldFileOperation()
{
    StreamReader reader = new StreamReader("file.txt");
    try
    {
        string content = reader.ReadToEnd();
        Console.WriteLine(content);
    }
    finally
    {
        if (reader != null)
        {
            reader.Close();
        }
    }
}

// 新代码
private void NewFileOperation()
{
    using (StreamReader reader = new StreamReader("file.txt"))
    {
        string content = reader.ReadToEnd();
        Console.WriteLine(content);
    }
    
    // 或者使用更现代的API
    string content2 = File.ReadAllText("file.txt");
    Console.WriteLine(content2);
}
```

**Pattern 4:** 依赖项升级
```xml
<!-- 依赖项升级示例 -->
<!-- 旧的包引用 -->
<ItemGroup>
  <PackageReference Include="Newtonsoft.Json" Version="10.0.3" />
  <PackageReference Include="NLog" Version="4.5.10" />
  <PackageReference Include="EntityFramework" Version="6.2.0" />
</ItemGroup>

<!-- 新的包引用 -->
<ItemGroup>
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
  <PackageReference Include="NLog" Version="5.2.8" />
  <PackageReference Include="Microsoft.EntityFrameworkCore" Version="8.0.0" />
  <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.0" />
</ItemGroup>

<!-- 依赖项升级脚本 -->
# 依赖项升级脚本
# 用于升级项目中的NuGet包

# 步骤1: 列出当前包
Write-Host "当前包列表:" -ForegroundColor Green
dotnet list package

# 步骤2: 升级所有包到最新版本
Write-Host "\n升级所有包到最新版本..." -ForegroundColor Green
dotnet add package Newtonsoft.Json --version 13.0.3
dotnet add package NLog --version 5.2.8
dotnet add package Microsoft.EntityFrameworkCore --version 8.0.0
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 8.0.0

# 步骤3: 验证升级结果
Write-Host "\n升级后的包列表:" -ForegroundColor Green
dotnet list package

# 步骤4: 清理和构建
Write-Host "\n清理和构建项目..." -ForegroundColor Green
dotnet clean
dotnet build
```

**Pattern 5:** 现代化改造
```csharp
// 现代化改造示例

// 1. 使用空值类型和空值检查
// 旧代码
private string GetUserName(User user)
{
    if (user != null && user.Name != null)
    {
        return user.Name;
    }
    return "未知";
}

// 新代码
private string GetUserName(User? user)
{
    return user?.Name ?? "未知";
}

// 2. 使用异步编程
// 旧代码
private void LoadData()
{
    // 同步加载数据
    var data = Database.GetData();
    BindData(data);
}

// 新代码
private async void LoadDataAsync()
{
    // 异步加载数据
    var data = await Database.GetDataAsync();
    BindData(data);
}

// 3. 使用依赖注入
// 旧代码
private void InitializeServices()
{
    _databaseService = new DatabaseService();
    _loggerService = new LoggerService();
}

// 新代码
// 在Program.cs中配置依赖注入
public static class Program
{
    [STAThread]
    static void Main()
    {
        var services = new ServiceCollection();
        ConfigureServices(services);
        
        var serviceProvider = services.BuildServiceProvider();
        
        Application.EnableVisualStyles();
        Application.SetCompatibleTextRenderingDefault(false);
        Application.Run(serviceProvider.GetRequiredService<MainForm>());
    }
    
    private static void ConfigureServices(ServiceCollection services)
    {
        services.AddTransient<MainForm>();
        services.AddScoped<IDatabaseService, DatabaseService>();
        services.AddScoped<ILoggerService, LoggerService>();
    }
}

// 在Form中使用依赖注入
public class MainForm : Form
{
    private readonly IDatabaseService _databaseService;
    private readonly ILoggerService _loggerService;
    
    public MainForm(IDatabaseService databaseService, ILoggerService loggerService)
    {
        _databaseService = databaseService;
        _loggerService = loggerService;
        InitializeComponent();
    }
}

// 4. 使用现代C#特性
// 旧代码
private class User
{
    private string _name;
    private int _age;
    
    public string Name
    {
        get { return _name; }
        set { _name = value; }
    }
    
    public int Age
    {
        get { return _age; }
        set { _age = value; }
    }
    
    public User(string name, int age)
    {
        _name = name;
        _age = age;
    }
}

// 新代码
private record User(string Name, int Age);

// 5. 使用现代配置系统
// 旧代码
private void LoadConfiguration()
{
    var config = ConfigurationManager.AppSettings["SettingName"];
}

// 新代码
private void LoadConfiguration()
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(AppDomain.CurrentDomain.BaseDirectory)
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true);
    
    var configuration = builder.Build();
    var config = configuration["SettingName"];
}

// appsettings.json
{
  "SettingName": "SettingValue",
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;"
  }
}
```

## Examples

### Example 1: .NET Framework到.NET 8+迁移
- Input: 需要将.NET Framework 4.8项目迁移到.NET 8
- Steps:
  1. 更新项目文件(.csproj)到新格式
  2. 更新Program.cs
  3. 检查并更新AssemblyInfo.cs
  4. 运行兼容性检查
  5. 解决发现的问题
  6. 测试迁移后的应用
- Expected output / acceptance: 应用成功迁移到.NET 8并正常运行

### Example 2: 兼容性检查
- Input: 需要检查项目的.NET兼容性
- Steps:
  1. 运行兼容性检查工具
  2. 分析生成的报告
  3. 解决发现的兼容性问题
  4. 重新运行检查确认问题已解决
- Expected output / acceptance: 兼容性问题得到解决

### Example 3: 旧API替换
- Input: 需要替换项目中的旧API
- Steps:
  1. 识别使用的旧API
  2. 替换为现代API
  3. 测试替换后的代码
- Expected output / acceptance: 旧API成功替换为现代API

### Example 4: 依赖项升级
- Input: 需要升级项目的依赖项
- Steps:
  1. 检查当前依赖项版本
  2. 升级到兼容的新版本
  3. 解决依赖项冲突
  4. 测试升级后的应用
- Expected output / acceptance: 依赖项成功升级到新版本

### Example 5: 现代化改造
- Input: 需要对项目进行现代化改造
- Steps:
  1. 识别需要现代化的代码部分
  2. 应用现代C#特性和模式
  3. 测试改造后的代码
- Expected output / acceptance: 代码成功现代化并保持功能正常

## References

- `references/index.md`: .NET迁移最佳实践导航
- `references/framework-migration.md`: .NET Framework到.NET 8+迁移指南
- `references/compatibility-check.md`: 兼容性检查指南
- `references/api-replacement.md`: 旧API替换指南
- `references/dependency-upgrade.md`: 依赖项升级指南
- `references/modernization.md`: 现代化改造指南

## Maintenance

- Sources: Microsoft官方文档和.NET迁移最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现
