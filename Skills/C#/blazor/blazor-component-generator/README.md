# Blazor组件生成器 (blazor-component-generator)

为C# Blazor项目提供专业的组件生成和配置能力，确保组件具有规范、一致、高效的实现。

## 主要功能

- **Razor组件编写**：编写和配置Razor组件
- **参数[Parameter]定义**：定义和使用组件参数
- **级联值传递**：传递和接收级联值
- **组件生命周期管理**：管理组件的生命周期
- **组件间通信**：实现组件之间的通信

## 使用场景

- 需要编写Razor组件
- 需要定义参数[Parameter]
- 需要传递级联值
- 需要管理组件生命周期
- 需要实现组件间通信
- 需要优化现有组件实现

## 快速开始

### Razor组件编写
```razor
<!-- 基本Razor组件 -->
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### 参数[Parameter]定义
```razor
<!-- 带参数的组件 -->
<div class="card">
    <div class="card-header">@Title</div>
    <div class="card-body">
        @ChildContent
    </div>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public EventCallback<MouseEventArgs> OnClick { get; set; }
}
```

### 级联值传递
```razor
<!-- 级联值提供 -->
<CascadingValue Value="this">
    <ChildComponent />
</CascadingValue>

<!-- 子组件接收级联值 -->
@code {
    [CascadingParameter]
    public ParentComponent Parent { get; set; }
}
```

### 组件生命周期管理
```razor
<!-- 组件生命周期 -->
@code {
    // 组件初始化
    protected override void OnInitialized()
    {
        Console.WriteLine("Component initialized");
        base.OnInitialized();
    }

    // 异步初始化
    protected override async Task OnInitializedAsync()
    {
        await LoadDataAsync();
        await base.OnInitializedAsync();
    }

    // 参数变更
    protected override void OnParametersSet()
    {
        Console.WriteLine("Parameters set");
        base.OnParametersSet();
    }

    // 组件渲染
    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            Console.WriteLine("First render completed");
        }
        base.OnAfterRender(firstRender);
    }

    // 组件卸载
    public void Dispose()
    {
        Console.WriteLine("Component disposed");
    }
}
```

### 组件间通信
```razor
<!-- 父子组件通信 -->
<!-- ParentComponent.razor -->
<ChildComponent OnValueChanged="HandleValueChanged" />

@code {
    private string childValue;

    public void HandleValueChanged(string value)
    {
        childValue = value;
        StateHasChanged();
    }
}

<!-- ChildComponent.razor -->
<input type="text" @bind="Value" @bind:event="oninput" />

@code {
    [Parameter]
    public EventCallback<string> OnValueChanged { get; set; }

    private string _value;
    public string Value
    {
        get => _value;
        set
        {
            if (_value != value)
            {
                _value = value;
                OnValueChanged.InvokeAsync(value);
            }
        }
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件