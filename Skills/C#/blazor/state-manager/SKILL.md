---
name: state-manager
description: "C# Blazor状态管理师技能：负责Fluxor状态容器配置、CascadingValue管理、组件状态同步、状态持久化、性能优化。使用时需要配置Fluxor状态容器、管理CascadingValue、同步组件状态、实现状态持久化、优化性能。"
---

# state-manager Skill

为C# Blazor项目提供专业的状态管理和配置能力，确保状态管理具有规范、一致、高效的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置Fluxor状态容器
- 需要管理CascadingValue
- 需要同步组件状态
- 需要实现状态持久化
- 需要优化性能
- 需要优化现有状态管理实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行状态管理设计决策
- 不处理状态管理的深度性能优化（仅设计层面）
- 不负责状态管理的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** Fluxor状态容器配置
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
public class DecrementCounterAction { }
public class ResetCounterAction { }

// Reducer定义
public class CounterReducer {
    [ReducerMethod]
    public static CounterState ReduceIncrementCounterAction(CounterState state, IncrementCounterAction action) {
        return new CounterState(state.Count + 1);
    }

    [ReducerMethod]
    public static CounterState ReduceDecrementCounterAction(CounterState state, DecrementCounterAction action) {
        return new CounterState(state.Count - 1);
    }

    [ReducerMethod]
    public static CounterState ReduceResetCounterAction(CounterState state, ResetCounterAction action) {
        return new CounterState(0);
    }
}

// Effect定义 (可选)
public class CounterEffects {
    [EffectMethod]
    public async Task HandleIncrementCounterAction(IncrementCounterAction action, IDispatcher dispatcher) {
        // 执行副作用操作
        await Task.Delay(100); // 模拟异步操作
        // 可以分发其他action
    }
}

// 在Program.cs中注册
public static async Task Main(string[] args)
{
    var builder = WebAssemblyHostBuilder.CreateDefault(args);
    builder.RootComponents.Add<App>("#app");

    // 注册Fluxor
    builder.Services.AddFluxor(options => {
        options.ScanAssemblies(typeof(Program).Assembly);
        options.UseReduxDevTools(); // 开发环境使用Redux DevTools
    });

    await builder.Build().RunAsync();
}

// 在组件中使用
@using Fluxor

@inject IState<CounterState> CounterState
@inject IDispatcher Dispatcher

<h1>Counter: @CounterState.Value.Count</h1>

<button @onclick="() => Dispatcher.Dispatch(new IncrementCounterAction())">Increment</button>
<button @onclick="() => Dispatcher.Dispatch(new DecrementCounterAction())">Decrement</button>
<button @onclick="() => Dispatcher.Dispatch(new ResetCounterAction())">Reset</button>
```

**Pattern 2:** CascadingValue管理
```razor
<!-- 级联值提供 -->
<CascadingValue Value="this" Name="AppState">
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingValue>

@code {
    private string _userName;
    public string UserName
    {
        get => _userName;
        set
        {
            if (_userName != value)
            {
                _userName = value;
                // 通知所有使用此级联值的组件
                StateHasChanged();
            }
        }
    }

    private bool _isLoggedIn;
    public bool IsLoggedIn
    {
        get => _isLoggedIn;
        set
        {
            if (_isLoggedIn != value)
            {
                _isLoggedIn = value;
                StateHasChanged();
            }
        }
    }
}

<!-- 子组件接收级联值 -->
@code {
    [CascadingParameter(Name = "AppState")]
    public App AppState { get; set; }

    // 使用级联值
    private string GetWelcomeMessage()
    {
        if (AppState.IsLoggedIn)
        {
            return $"Welcome back, {AppState.UserName}!";
        }
        return "Please log in.";
    }
}
```

**Pattern 3:** 组件状态同步
```razor
<!-- 组件状态同步 -->
@using Fluxor

@inject IState<CounterState> CounterState

<h3>Counter Component</h3>
<p>Current count: @CounterState.Value.Count</p>

@code {
    // 组件会自动响应状态变化
    // 不需要手动调用StateHasChanged()
}

<!-- 多个组件共享状态 -->
<!-- ComponentA.razor -->
@using Fluxor

@inject IState<UserState> UserState

<h3>User Info</h3>
<p>Name: @UserState.Value.Name</p>

<!-- ComponentB.razor -->
@using Fluxor

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

**Pattern 4:** 状态持久化
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

    public async Task RemoveStateAsync(string key)
    {
        await _jsRuntime.InvokeVoidAsync("localStorage.removeItem", key);
    }
}

// 在Fluxor中使用
public class CounterEffects {
    private readonly StatePersistenceService _persistenceService;

    public CounterEffects(StatePersistenceService persistenceService)
    {
        _persistenceService = persistenceService;
    }

    [EffectMethod]
    public async Task HandleIncrementCounterAction(IncrementCounterAction action, IDispatcher dispatcher) {
        // 执行副作用操作
        await Task.Delay(100);
    }

    [EffectMethod]
    public async Task HandleCounterStateChanged(CounterState state, IDispatcher dispatcher) {
        // 持久化状态
        await _persistenceService.SaveStateAsync("counterState", state);
    }
}

// 在应用启动时加载状态
public static async Task Main(string[] args)
{
    var builder = WebAssemblyHostBuilder.CreateDefault(args);
    builder.RootComponents.Add<App>("#app");

    // 注册服务
    builder.Services.AddSingleton<StatePersistenceService>();

    // 注册Fluxor
    builder.Services.AddFluxor(options => {
        options.ScanAssemblies(typeof(Program).Assembly);
    });

    var host = builder.Build();

    // 加载状态
    var persistenceService = host.Services.GetRequiredService<StatePersistenceService>();
    var counterState = await persistenceService.LoadStateAsync<CounterState>("counterState");
    if (counterState != null)
    {
        var dispatcher = host.Services.GetRequiredService<IDispatcher>();
        dispatcher.Dispatch(new LoadCounterStateAction(counterState.Count));
    }

    await host.RunAsync();
}
```

**Pattern 5:** 性能优化
```csharp
// 1. 使用[FeatureState]属性
[FeatureState]
public class CounterState {
    public int Count { get; } = 0;

    public CounterState(int count)
    {
        Count = count;
    }
}

// 2. 使用IState.Select()进行局部订阅
@using Fluxor

@inject IState<CounterState> CounterState

<h3>Counter Component</h3>
<p>Current count: @currentCount</p>

@code {
    private int currentCount;

    protected override void OnInitialized()
    {
        // 只订阅Count属性的变化
        CounterState.Select(s => s.Count).Subscribe(count => {
            currentCount = count;
            // 只有当Count变化时才会重新渲染
            StateHasChanged();
        });
    }
}

// 3. 使用ShouldRender()方法
protected override bool ShouldRender()
{
    // 自定义渲染逻辑
    // 只有当需要时才返回true
    return _shouldRender;
}

// 4. 使用CascadingValue的IsFixed属性
<CascadingValue Value="this" IsFixed="true">
    <ChildComponent />
</CascadingValue>

// 5. 批量更新状态
public class BatchUpdateAction { }

[ReducerMethod]
public static AppState ReduceBatchUpdateAction(AppState state, BatchUpdateAction action) {
    // 一次性更新多个状态属性
    return new AppState(
        new UserState("New Name", "new.email@example.com"),
        new SettingsState(true, "Dark")
    );
}
```

## Examples

### Example 1: 配置Fluxor状态容器
- Input: 需要为计数器应用配置Fluxor状态管理
- Steps:
  1. 安装Fluxor包
  2. 定义状态、Action、Reducer
  3. 注册Fluxor服务
  4. 在组件中使用状态
  5. 测试状态管理功能
- Expected output / acceptance: Fluxor状态管理正确配置和使用

### Example 2: 管理CascadingValue
- Input: 需要在组件树中共享应用状态
- Steps:
  1. 在顶层组件中提供CascadingValue
  2. 在子组件中接收级联值
  3. 使用级联值共享状态
  4. 测试级联值传递
- Expected output / acceptance: CascadingValue正确管理和使用

### Example 3: 实现状态持久化
- Input: 需要持久化用户设置状态
- Steps:
  1. 创建状态持久化服务
  2. 实现本地存储操作
  3. 在Fluxor Effect中使用持久化服务
  4. 在应用启动时加载状态
  5. 测试状态持久化功能
- Expected output / acceptance: 状态正确持久化和加载

## References

- `references/index.md`: 状态管理最佳实践导航
- `references/fluxor-config.md`: Fluxor状态容器配置指南
- `references/cascading-value.md`: CascadingValue管理指南
- `references/state-sync.md`: 组件状态同步指南
- `references/state-persistence.md`: 状态持久化指南
- `references/performance-optimization.md`: 性能优化指南

## Maintenance

- Sources: Blazor官方文档和Fluxor文档
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现