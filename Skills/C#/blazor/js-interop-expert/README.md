# JS互操作专家 (js-interop-expert)

为C# Blazor项目提供专业的JavaScript互操作和配置能力，确保JS互操作具有规范、一致、高效的实现。

## 主要功能

- **JSRuntime调用**：调用和使用JSRuntime
- **IJSObjectReference管理**：管理JavaScript对象引用
- **JavaScript隔离模块**：使用和管理JavaScript隔离模块
- **.NET-JS类型映射**：处理.NET和JavaScript之间的类型映射
- **异步互操作**：实现异步JavaScript互操作

## 使用场景

- 需要调用JSRuntime
- 需要管理IJSObjectReference
- 需要使用JavaScript隔离模块
- 需要处理.NET-JS类型映射
- 需要实现异步互操作
- 需要优化现有JS互操作实现

## 快速开始

### JSRuntime调用
```csharp
// 基本JSRuntime调用
@inject IJSRuntime JSRuntime

<button @onclick="ShowAlert">Show Alert</button>

@code {
    private async Task ShowAlert()
    {
        await JSRuntime.InvokeVoidAsync("alert", "Hello from Blazor!");
    }
}
```

### IJSObjectReference管理
```csharp
// IJSObjectReference管理
@inject IJSRuntime JSRuntime

<button @onclick="InitializeChart">Initialize Chart</button>

@code {
    private IJSObjectReference _chartModule;
    private IJSObjectReference _chartInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // 加载Chart.js模块
            _chartModule = await JSRuntime.InvokeAsync<IJSObjectReference>(
                "import", 
                "./js/chart.js"
            );
        }
    }

    private async Task InitializeChart()
    {
        if (_chartModule == null)
        {
            return;
        }

        // 创建图表实例
        _chartInstance = await _chartModule.InvokeAsync<IJSObjectReference>(
            "createChart", 
            "chartContainer"
        );
    }
}
```

### JavaScript隔离模块
```csharp
// JavaScript隔离模块
@inject IJSRuntime JSRuntime

<button @onclick="CallModuleFunction">Call Module Function</button>

@code {
    private IJSObjectReference _module;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // 加载隔离模块
            _module = await JSRuntime.InvokeAsync<IJSObjectReference>(
                "import", 
                "./js/mymodule.js"
            );
        }
    }

    private async Task CallModuleFunction()
    {
        if (_module == null)
        {
            return;
        }

        var message = await _module.InvokeAsync<string>("sayHello", "Blazor");
        Console.WriteLine(message);
    }
}
```

### .NET-JS类型映射
```csharp
// .NET-JS类型映射
@inject IJSRuntime JSRuntime

<button @onclick="PassComplexObject">Pass Complex Object</button>

@code {
    private class Person
    {
        public string Name { get; set; }
        public int Age { get; set; }
    }

    private async Task PassComplexObject()
    {
        var person = new Person
        {
            Name = "John Doe",
            Age = 30
        };

        await JSRuntime.InvokeVoidAsync("processPerson", person);
    }
}
```

### 异步互操作
```csharp
// 异步互操作
@inject IJSRuntime JSRuntime

<button @onclick="StartLongOperation">Start Long Operation</button>
<p>Progress: @progress%</p>

@code {
    private int progress = 0;

    private async Task StartLongOperation()
    {
        // 创建进度回调
        var progressCallback = new DotNetObjectReference<ProgressHandler>(new ProgressHandler(this));

        await JSRuntime.InvokeVoidAsync("longOperation", progressCallback);
    }

    public void UpdateProgress(int value)
    {
        progress = value;
        StateHasChanged();
    }

    private class ProgressHandler
    {
        private readonly js_interop_expert _parent;

        public ProgressHandler(js_interop_expert parent)
        {
            _parent = parent;
        }

        [JSInvokable]
        public void OnProgress(int value)
        {
            _parent.UpdateProgress(value);
        }
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件