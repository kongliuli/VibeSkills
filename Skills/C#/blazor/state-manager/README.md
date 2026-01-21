# 状态管理师 (state-manager)

为C# Blazor项目提供专业的状态管理和配置能力，确保状态管理具有规范、一致、高效的实现。

## 主要功能

- **Fluxor状态容器配置**：配置和管理Fluxor状态容器
- **CascadingValue管理**：管理和使用级联值
- **组件状态同步**：同步组件之间的状态
- **状态持久化**：实现状态的持久化存储
- **性能优化**：优化状态管理的性能

## 使用场景

- 需要配置Fluxor状态容器
- 需要管理CascadingValue
- 需要同步组件状态
- 需要实现状态持久化
- 需要优化性能
- 需要优化现有状态管理实现

## 快速开始

### Fluxor状态容器配置
```csharp
// 安装Fluxor包: Install-Package Fluxor

// 状态定义
public class CounterState
{
    public int Count { get; } = 0;

    public CounterState(int count)
    {
        Count = count;
    }
}

// Action定义
public class IncrementCounterAction { }

// Reducer定义
public class CounterReducer {
    [ReducerMethod]
    public static CounterState ReduceIncrementCounterAction(CounterState state, IncrementCounterAction action) {
        return new CounterState(state.Count + 1);
    }
}

// 在Program.cs中注册
builder.Services.AddFluxor(options => {
    options.ScanAssemblies(typeof(Program).Assembly);
});

// 在组件中使用
@inject IState<CounterState> CounterState
@inject IDispatcher Dispatcher

<h1>Counter: @CounterState.Value.Count</h1>
<button @onclick="() => Dispatcher.Dispatch(new IncrementCounterAction())">Increment</button>
```

### CascadingValue管理
```razor
<!-- 级联值提供 -->
<CascadingValue Value="this" Name="AppState">
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
        </Found>
    </Router>
</CascadingValue>

@code {
    public string UserName { get; set; }
    public bool IsLoggedIn { get; set; }
}

<!-- 子组件接收级联值 -->
@code {
    [CascadingParameter(Name = "AppState")]
    public App AppState { get; set; }
}
```

### 组件状态同步
```razor
<!-- 多个组件共享状态 -->
<!-- ComponentA.razor -->
@inject IState<UserState> UserState

<h3>User Info</h3>
<p>Name: @UserState.Value.Name</p>

<!-- ComponentB.razor -->
@inject IState<UserState> UserState
@inject IDispatcher Dispatcher

<h3>User Editor</h3>
<input type="text" @bind="userName" @bind:event="oninput" />
<button @onclick="UpdateUser">Update</button>

@code {
    private string userName;

    protected override void OnInitialized()
    {
        userName = UserState.Value.Name;
    }

    private void UpdateUser()
    {
        Dispatcher.Dispatch(new UpdateUserAction(userName));
    }
}
```

### 状态持久化
```csharp
// 状态持久化服务
public class StatePersistenceService
{
    private readonly IJSRuntime _jsRuntime;

    public StatePersistenceService(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task SaveStateAsync<T>(string key, T state)
    {
        var json = JsonSerializer.Serialize(state);
        await _jsRuntime.InvokeVoidAsync("localStorage.setItem", key, json);
    }

    public async Task<T> LoadStateAsync<T>(string key)
    {
        var json = await _jsRuntime.InvokeAsync<string>("localStorage.getItem", key);
        if (string.IsNullOrEmpty(json))
        {
            return default;
        }
        return JsonSerializer.Deserialize<T>(json);
    }
}
```

### 性能优化
```csharp
// 使用IState.Select()进行局部订阅
@inject IState<CounterState> CounterState

<p>Current count: @currentCount</p>

@code {
    private int currentCount;

    protected override void OnInitialized()
    {
        // 只订阅Count属性的变化
        CounterState.Select(s => s.Count).Subscribe(count => {
            currentCount = count;
            StateHasChanged();
        });
    }
}

// 使用ShouldRender()方法
protected override bool ShouldRender()
{
    // 自定义渲染逻辑
    return _shouldRender;
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件