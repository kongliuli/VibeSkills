---
name: blazor-component-generator
description: "C# Blazor组件生成器技能：负责Razor组件编写、参数[Parameter]定义、级联值传递、组件生命周期管理、组件间通信。使用时需要编写Razor组件、定义参数[Parameter]、传递级联值、管理组件生命周期、实现组件间通信。"
---

# blazor-component-generator Skill

为C# Blazor项目提供专业的组件生成和配置能力，确保组件具有规范、一致、高效的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要编写Razor组件
- 需要定义参数[Parameter]
- 需要传递级联值
- 需要管理组件生命周期
- 需要实现组件间通信
- 需要优化现有组件实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行组件设计决策
- 不处理组件的深度性能优化（仅设计层面）
- 不负责组件的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** Razor组件编写
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

<!-- 带参数的组件 -->
<div class="card">
    <div class="card-header">@Title</div>
    <div class="card-body">
        @ChildContent
    </div>
    @if (ShowFooter)
    {
        <div class="card-footer">
            @FooterContent
        </div>
    }
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public RenderFragment FooterContent { get; set; }

    [Parameter]
    public bool ShowFooter { get; set; } = false;
}
```

**Pattern 2:** 参数[Parameter]定义
```razor
<!-- 参数定义 -->
@code {
    // 基本参数
    [Parameter]
    public string Name { get; set; }

    // 必需参数
    [Parameter, EditorRequired]
    public int Id { get; set; }

    // 参数变更回调
    [Parameter]
    public EventCallback<string> OnNameChanged { get; set; }

    // 复杂类型参数
    [Parameter]
    public User User { get; set; }

    // 集合参数
    [Parameter]
    public List<Item> Items { get; set; }

    // 回调参数
    [Parameter]
    public EventCallback<MouseEventArgs> OnClick { get; set; }

    // 参数变更处理
    private string _name;
    [Parameter]
    public string Name
    {
        get => _name;
        set
        {
            if (_name != value)
            {
                _name = value;
                // 处理参数变更
                OnNameChanged.InvokeAsync(value);
            }
        }
    }
}
```

**Pattern 3:** 级联值传递
```razor
<!-- 级联值提供 -->
<CascadingValue Value="this">
    <ChildComponent />
</CascadingValue>

<!-- 多个级联值 -->
<CascadingValue Value="CurrentUser" Name="User">
    <CascadingValue Value="Theme" Name="Theme">
        <ChildComponent />
    </CascadingValue>
</CascadingValue>

<!-- 子组件接收级联值 -->
@code {
    [CascadingParameter]
    public ParentComponent Parent { get; set; }

    [CascadingParameter(Name = "User")]
    public User CurrentUser { get; set; }

    [CascadingParameter(Name = "Theme")]
    public Theme Theme { get; set; }
}
```

**Pattern 4:** 组件生命周期管理
```razor
<!-- 组件生命周期 -->
@implements IDisposable

<h3>@Title</h3>

@code {
    [Parameter]
    public string Title { get; set; }

    private Timer _timer;
    private int _count = 0;

    // 组件初始化
    protected override void OnInitialized()
    {
        Console.WriteLine("Component initialized");
        base.OnInitialized();
    }

    // 异步初始化
    protected override async Task OnInitializedAsync()
    {
        Console.WriteLine("Component initialized asynchronously");
        // 执行异步操作
        await LoadDataAsync();
        await base.OnInitializedAsync();
    }

    // 参数变更
    protected override void OnParametersSet()
    {
        Console.WriteLine("Parameters set");
        base.OnParametersSet();
    }

    // 异步参数变更
    protected override async Task OnParametersSetAsync()
    {
        Console.WriteLine("Parameters set asynchronously");
        await base.OnParametersSetAsync();
    }

    // 组件渲染
    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            Console.WriteLine("First render completed");
            // 初始化Timer
            _timer = new Timer(1000);
            _timer.Elapsed += TimerElapsed;
            _timer.Start();
        }
        base.OnAfterRender(firstRender);
    }

    // 异步渲染
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            Console.WriteLine("First render completed asynchronously");
            // 执行需要DOM的异步操作
            await JSRuntime.InvokeVoidAsync("console.log", "First render");
        }
        await base.OnAfterRenderAsync(firstRender);
    }

    // 组件卸载
    public void Dispose()
    {
        Console.WriteLine("Component disposed");
        _timer?.Dispose();
        GC.SuppressFinalize(this);
    }

    private void TimerElapsed(object sender, ElapsedEventArgs e)
    {
        _count++;
        // 触发组件重新渲染
        StateHasChanged();
    }

    private async Task LoadDataAsync()
    {
        // 模拟数据加载
        await Task.Delay(1000);
    }
}
```

**Pattern 5:** 组件间通信
```razor
<!-- 父子组件通信 -->
<!-- ParentComponent.razor -->
<h3>Parent Component</h3>
<p>Child value: @childValue</p>
<ChildComponent Parent="this" OnValueChanged="HandleValueChanged" />

@code {
    private string childValue;

    public void HandleValueChanged(string value)
    {
        childValue = value;
        StateHasChanged();
    }
}

<!-- ChildComponent.razor -->
<h4>Child Component</h4>
<input type="text" @bind="Value" @bind:event="oninput" />

@code {
    [Parameter]
    public ParentComponent Parent { get; set; }

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

<!-- 同级组件通信 (使用EventCallback) -->
<!-- ComponentA.razor -->
<button @onclick="() => OnAction.InvokeAsync("Action from A")">
    Trigger Action
</button>

@code {
    [Parameter]
    public EventCallback<string> OnAction { get; set; }
}

<!-- ComponentB.razor -->
<p>Last action: @lastAction</p>

@code {
    [Parameter]
    public string LastAction { get; set; }

    private string lastAction;

    protected override void OnParametersSet()
    {
        lastAction = LastAction;
        base.OnParametersSet();
    }
}

<!-- ParentComponent.razor -->
<ComponentA OnAction="HandleAction" />
<ComponentB LastAction="lastAction" />

@code {
    private string lastAction;

    private void HandleAction(string action)
    {
        lastAction = action;
        StateHasChanged();
    }
}
```

## Examples

### Example 1: 编写基本Razor组件
- Input: 需要创建一个显示计数器的组件
- Steps:
  1. 创建Counter.razor文件
  2. 添加页面指令和HTML结构
  3. 实现计数器逻辑
  4. 测试组件功能
- Expected output / acceptance: 组件正确显示和响应

### Example 2: 定义和使用参数
- Input: 需要创建一个带参数的卡片组件
- Steps:
  1. 创建Card.razor文件
  2. 定义Title、ChildContent等参数
  3. 在父组件中使用Card组件
  4. 测试参数传递
- Expected output / acceptance: 参数正确传递和显示

### Example 3: 使用级联值
- Input: 需要在组件树中传递用户信息
- Steps:
  1. 在顶层组件中提供CascadingValue
  2. 在子组件中接收级联值
  3. 使用级联值显示用户信息
  4. 测试级联值传递
- Expected output / acceptance: 级联值正确传递和使用

## References

- `references/index.md`: Blazor组件开发最佳实践导航
- `references/razor-components.md`: Razor组件编写指南
- `references/parameters.md`: 参数定义和使用指南
- `references/cascading-values.md`: 级联值传递指南
- `references/lifecycle.md`: 组件生命周期管理指南
- `references/component-communication.md`: 组件间通信指南

## Maintenance

- Sources: Blazor官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现