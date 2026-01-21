---
name: js-interop-expert
description: "C# Blazor JS互操作专家技能：负责JSRuntime调用、IJSObjectReference管理、JavaScript隔离模块、.NET-JS类型映射、异步互操作。使用时需要调用JSRuntime、管理IJSObjectReference、使用JavaScript隔离模块、处理.NET-JS类型映射、实现异步互操作。"
---

# js-interop-expert Skill

为C# Blazor项目提供专业的JavaScript互操作和配置能力，确保JS互操作具有规范、一致、高效的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要调用JSRuntime
- 需要管理IJSObjectReference
- 需要使用JavaScript隔离模块
- 需要处理.NET-JS类型映射
- 需要实现异步互操作
- 需要优化现有JS互操作实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行JS互操作设计决策
- 不处理JS互操作的深度性能优化（仅设计层面）
- 不负责JS互操作的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** JSRuntime调用
```csharp
// 基本JSRuntime调用
@inject IJSRuntime JSRuntime

<button @onclick="ShowAlert">Show Alert</button>
<button @onclick="GetBrowserInfo">Get Browser Info</button>

@code {
    private async Task ShowAlert()
    {
        await JSRuntime.InvokeVoidAsync("alert", "Hello from Blazor!");
    }

    private async Task GetBrowserInfo()
    {
        var browserName = await JSRuntime.InvokeAsync<string>("navigator.userAgent");
        Console.WriteLine($"Browser: {browserName}");
    }
}

// 调用自定义JavaScript函数
// 在wwwroot/index.html中添加
<script>
    window.showMessage = function(message) {
        alert(message);
    };

    window.calculateSum = function(a, b) {
        return a + b;
    };
</script>

// 在Blazor中调用
private async Task CallCustomFunction()
{
    await JSRuntime.InvokeVoidAsync("showMessage", "Custom message");
    var sum = await JSRuntime.InvokeAsync<int>("calculateSum", 5, 10);
    Console.WriteLine($"Sum: {sum}");
}
```

**Pattern 2:** IJSObjectReference管理
```csharp
// IJSObjectReference管理
@inject IJSRuntime JSRuntime

<button @onclick="InitializeChart">Initialize Chart</button>
<button @onclick="UpdateChart">Update Chart</button>
<button @onclick="DisposeChart">Dispose Chart</button>

<div id="chartContainer" style="width: 400px; height: 300px;"></div>

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
            "chartContainer",
            new {
                type = "bar",
                data = new {
                    labels = new[] { "Red", "Blue", "Yellow", "Green", "Purple", "Orange" },
                    datasets = new[] {
                        new {
                            label = "Votes",
                            data = new[] { 12, 19, 3, 5, 2, 3 },
                            backgroundColor = new[] {
                                "rgba(255, 99, 132, 0.2)",
                                "rgba(54, 162, 235, 0.2)",
                                "rgba(255, 206, 86, 0.2)",
                                "rgba(75, 192, 192, 0.2)",
                                "rgba(153, 102, 255, 0.2)",
                                "rgba(255, 159, 64, 0.2)"
                            }
                        }
                    }
                }
            }
        );
    }

    private async Task UpdateChart()
    {
        if (_chartInstance != null)
        {
            // 更新图表数据
            await _chartInstance.InvokeVoidAsync(
                "update",
                new {
                    data = new {
                        datasets = new[] {
                            new {
                                data = new[] { 15, 25, 8, 12, 7, 10 }
                            }
                        }
                    }
                }
            );
        }
    }

    private async Task DisposeChart()
    {
        if (_chartInstance != null)
        {
            await _chartInstance.InvokeVoidAsync("destroy");
            await _chartInstance.DisposeAsync();
            _chartInstance = null;
        }
    }

    public async ValueTask DisposeAsync()
    {
        if (_chartInstance != null)
        {
            await _chartInstance.DisposeAsync();
        }
        if (_chartModule != null)
        {
            await _chartModule.DisposeAsync();
        }
    }
}

// wwwroot/js/chart.js
import Chart from 'https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js';

export function createChart(containerId, config) {
    const ctx = document.getElementById(containerId).getContext('2d');
    return new Chart(ctx, config);
}
```

**Pattern 3:** JavaScript隔离模块
```csharp
// JavaScript隔离模块
// wwwroot/js/mymodule.js
export function sayHello(name) {
    return `Hello, ${name}!`;
}

export function calculateFactorial(n) {
    if (n <= 1) return 1;
    return n * calculateFactorial(n - 1);
}

export class Calculator {
    add(a, b) {
        return a + b;
    }
    subtract(a, b) {
        return a - b;
    }
}

// 在Blazor中使用
@inject IJSRuntime JSRuntime

<button @onclick="CallModuleFunction">Call Module Function</button>
<button @onclick="UseCalculatorClass">Use Calculator Class</button>

@code {
    private IJSObjectReference _module;
    private IJSObjectReference _calculator;

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

        var factorial = await _module.InvokeAsync<int>("calculateFactorial", 5);
        Console.WriteLine($"Factorial of 5: {factorial}");
    }

    private async Task UseCalculatorClass()
    {
        if (_module == null)
        {
            return;
        }

        // 创建Calculator实例
        if (_calculator == null)
        {
            _calculator = await _module.InvokeAsync<IJSObjectReference>("new", "Calculator");
        }

        // 调用实例方法
        var sum = await _calculator.InvokeAsync<int>("add", 10, 5);
        var difference = await _calculator.InvokeAsync<int>("subtract", 10, 5);

        Console.WriteLine($"Sum: {sum}, Difference: {difference}");
    }

    public async ValueTask DisposeAsync()
    {
        if (_calculator != null)
        {
            await _calculator.DisposeAsync();
        }
        if (_module != null)
        {
            await _module.DisposeAsync();
        }
    }
}
```

**Pattern 4:** .NET-JS类型映射
```csharp
// .NET-JS类型映射
@inject IJSRuntime JSRuntime

<button @onclick="PassComplexObject">Pass Complex Object</button>
<button @onclick="ReceiveComplexObject">Receive Complex Object</button>

@code {
    private class Person
    {
        public string Name { get; set; }
        public int Age { get; set; }
        public List<string> Hobbies { get; set; }
    }

    private async Task PassComplexObject()
    {
        var person = new Person
        {
            Name = "John Doe",
            Age = 30,
            Hobbies = new List<string> { "Reading", "Gaming", "Cooking" }
        };

        await JSRuntime.InvokeVoidAsync("processPerson", person);
    }

    private async Task ReceiveComplexObject()
    {
        var person = await JSRuntime.InvokeAsync<Person>("getPerson");
        Console.WriteLine($"Name: {person.Name}, Age: {person.Age}");
        Console.WriteLine("Hobbies: " + string.Join(", ", person.Hobbies));
    }
}

// 在wwwroot/index.html中添加
<script>
    window.processPerson = function(person) {
        console.log('Person received from Blazor:', person);
        console.log('Name:', person.name); // 注意：属性名会转换为小写
        console.log('Age:', person.age);
        console.log('Hobbies:', person.hobbies);
    };

    window.getPerson = function() {
        return {
            name: "Jane Smith",
            age: 25,
            hobbies: ["Hiking", "Painting", "Traveling"]
        };
    };
</script>
```

**Pattern 5:** 异步互操作
```csharp
// 异步互操作
@inject IJSRuntime JSRuntime

<button @onclick="StartLongOperation">Start Long Operation</button>
<p>Operation status: @operationStatus</p>
<p>Progress: @progress%</p>

@code {
    private string operationStatus = "Ready";
    private int progress = 0;
    private IJSObjectReference _interopModule;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            _interopModule = await JSRuntime.InvokeAsync<IJSObjectReference>(
                "import", 
                "./js/interop.js"
            );
        }
    }

    private async Task StartLongOperation()
    {
        if (_interopModule == null)
        {
            return;
        }

        operationStatus = "Running";
        progress = 0;

        try
        {
            // 创建进度回调
            var progressCallback = new DotNetObjectReference<ProgressHandler>(new ProgressHandler(this));

            // 执行长时间操作
            var result = await _interopModule.InvokeAsync<string>(
                "longRunningOperation", 
                progressCallback
            );

            operationStatus = $"Completed: {result}";
        }
        catch (Exception ex)
        {
            operationStatus = $"Error: {ex.Message}";
        }
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

// wwwroot/js/interop.js
export async function longRunningOperation(progressCallback) {
    return new Promise((resolve) => {
        let progress = 0;
        const interval = setInterval(() => {
            progress += 10;
            // 调用.NET回调
            progressCallback.invokeMethodAsync('OnProgress', progress);
            
            if (progress >= 100) {
                clearInterval(interval);
                resolve('Operation completed successfully!');
            }
        }, 500);
    });
}
```

## Examples

### Example 1: 基本JSRuntime调用
- Input: 需要调用浏览器的alert函数
- Steps:
  1. 注入IJSRuntime
  2. 调用InvokeVoidAsync方法
  3. 传递函数名和参数
  4. 测试调用结果
- Expected output / acceptance: 正确显示alert对话框

### Example 2: 使用JavaScript隔离模块
- Input: 需要使用自定义JavaScript模块
- Steps:
  1. 创建JavaScript模块文件
  2. 导出函数和类
  3. 在Blazor中加载模块
  4. 调用模块中的函数
  5. 测试模块功能
- Expected output / acceptance: 正确加载和使用JavaScript模块

### Example 3: 实现进度回调
- Input: 需要从JavaScript向Blazor传递进度信息
- Steps:
  1. 创建.NET回调对象
  2. 在JavaScript中调用回调方法
  3. 在Blazor中处理回调
  4. 更新UI显示进度
  5. 测试进度传递
- Expected output / acceptance: 正确传递和显示进度信息

## References

- `references/index.md`: JS互操作最佳实践导航
- `references/jsruntime.md`: JSRuntime调用指南
- `references/jsobjectreference.md`: IJSObjectReference管理指南
- `references/isolated-modules.md`: JavaScript隔离模块指南
- `references/type-mapping.md`: .NET-JS类型映射指南
- `references/async-interop.md`: 异步互操作指南

## Maintenance

- Sources: Blazor官方文档和JavaScript互操作最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现