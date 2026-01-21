---
name: winforms-event-handler
description: "C# WinForms事件处理器技能：负责事件绑定管理、异步事件处理、事件链管理、事件参数设计、控件事件继承。使用时需要管理事件绑定、处理异步事件、管理事件链、设计事件参数、实现控件事件继承。"
---

# winforms-event-handler Skill

为C# WinForms项目提供专业的事件处理和管理能力，确保应用能够正确处理各种事件场景，包括事件绑定、异步处理、事件链管理、参数设计和事件继承。

## When to Use This Skill

Trigger when any of these applies:
- 需要管理事件绑定
- 需要处理异步事件
- 需要管理事件链
- 需要设计事件参数
- 需要实现控件事件继承
- 需要优化现有事件处理实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行事件设计决策
- 不处理事件的深度性能优化（仅设计层面）
- 不负责事件的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** 事件绑定管理
```csharp
// 事件绑定管理
public class MainForm : Form
{
    private Button _button;
    private TextBox _textBox;

    public MainForm()
    {
        InitializeComponent();
        InitializeControls();
    }

    private void InitializeControls()
    {
        // 创建控件
        _button = new Button
        {
            Text = "点击我",
            Location = new Point(10, 10),
            Size = new Size(100, 30)
        };

        _textBox = new TextBox
        {
            Text = "输入文本",
            Location = new Point(10, 50),
            Size = new Size(200, 30)
        };

        // 添加控件到表单
        Controls.Add(_button);
        Controls.Add(_textBox);

        // 绑定事件
        BindEvents();
    }

    private void BindEvents()
    {
        // 使用方法组绑定
        _button.Click += Button_Click;
        
        // 使用lambda表达式绑定
        _textBox.TextChanged += (sender, e) =>
        {
            Console.WriteLine($"文本变更: {_textBox.Text}");
        };

        // 使用匿名方法绑定
        _textBox.KeyPress += delegate (object sender, KeyPressEventArgs e)
        {
            if (e.KeyChar == (char)Keys.Enter)
            {
                Console.WriteLine("按下了Enter键");
                e.Handled = true;
            }
        };
    }

    private void Button_Click(object sender, EventArgs e)
    {
        Console.WriteLine("按钮被点击");
        _textBox.Text = "按钮被点击";
    }

    // 解绑事件
    private void UnbindEvents()
    {
        _button.Click -= Button_Click;
        // 注意：lambda表达式和匿名方法无法直接解绑
    }

    protected override void Dispose(bool disposing)
    {
        if (disposing)
        {
            UnbindEvents();
            _button?.Dispose();
            _textBox?.Dispose();
        }
        base.Dispose(disposing);
    }
}
```

**Pattern 2:** 异步事件处理
```csharp
// 异步事件处理
public class MainForm : Form
{
    private Button _asyncButton;
    private Label _statusLabel;
    private CancellationTokenSource _cts;

    public MainForm()
    {
        InitializeComponent();
        InitializeControls();
    }

    private void InitializeControls()
    {
        _asyncButton = new Button
        {
            Text = "执行异步操作",
            Location = new Point(10, 10),
            Size = new Size(150, 30)
        };

        _statusLabel = new Label
        {
            Text = "就绪",
            Location = new Point(10, 50),
            Size = new Size(200, 20)
        };

        Controls.Add(_asyncButton);
        Controls.Add(_statusLabel);

        // 绑定异步事件处理程序
        _asyncButton.Click += AsyncButton_Click;
    }

    // 使用async/await处理异步事件
    private async void AsyncButton_Click(object sender, EventArgs e)
    {
        // 禁用按钮，防止重复点击
        _asyncButton.Enabled = false;
        _statusLabel.Text = "执行中...";

        try
        {
            // 取消令牌源
            _cts = new CancellationTokenSource();
            
            // 执行异步操作
            string result = await PerformAsyncOperation(_cts.Token);
            
            // 更新UI
            _statusLabel.Text = $"完成: {result}";
        }
        catch (OperationCanceledException)
        {
            _statusLabel.Text = "操作已取消";
        }
        catch (Exception ex)
        {
            _statusLabel.Text = $"错误: {ex.Message}";
        }
        finally
        {
            // 重新启用按钮
            _asyncButton.Enabled = true;
            _cts?.Dispose();
        }
    }

    private async Task<string> PerformAsyncOperation(CancellationToken cancellationToken)
    {
        // 模拟长时间运行的操作
        await Task.Delay(3000, cancellationToken);
        return "异步操作完成";
    }

    // 取消操作的方法
    private void CancelOperation()
    {
        _cts?.Cancel();
    }
}
```

**Pattern 3:** 事件链管理
```csharp
// 事件链管理
public class EventChainManager
{
    // 定义事件链
    public event EventHandler<ChainEventArgs> ChainEvent;

    // 触发事件链
    public void TriggerChainEvent(string data)
    {
        var args = new ChainEventArgs(data);
        OnChainEvent(args);
    }

    // 事件触发方法
    protected virtual void OnChainEvent(ChainEventArgs e)
    {
        // 事件链处理
        Console.WriteLine($"事件链开始: {e.Data}");
        
        // 触发事件
        ChainEvent?.Invoke(this, e);
        
        // 事件链结束处理
        if (!e.Canceled)
        {
            Console.WriteLine($"事件链完成: {e.Data}");
        }
        else
        {
            Console.WriteLine($"事件链被取消: {e.Data}");
        }
    }
}

// 事件参数
public class ChainEventArgs : EventArgs
{
    public string Data { get; }
    public bool Canceled { get; set; }

    public ChainEventArgs(string data)
    {
        Data = data;
        Canceled = false;
    }
}

// 使用示例
public class MainForm : Form
{
    private EventChainManager _eventChainManager;

    public MainForm()
    {
        InitializeComponent();
        
        _eventChainManager = new EventChainManager();
        
        // 注册多个事件处理程序，形成事件链
        _eventChainManager.ChainEvent += EventHandler1;
        _eventChainManager.ChainEvent += EventHandler2;
        _eventChainManager.ChainEvent += EventHandler3;
    }

    private void TriggerChainEvent(object sender, EventArgs e)
    {
        _eventChainManager.TriggerChainEvent("测试数据");
    }

    private void EventHandler1(object sender, ChainEventArgs e)
    {
        Console.WriteLine($"处理器1: {e.Data}");
        // 可以修改事件参数
        e.Data += "-处理器1修改";
    }

    private void EventHandler2(object sender, ChainEventArgs e)
    {
        Console.WriteLine($"处理器2: {e.Data}");
        // 可以取消事件链
        // e.Canceled = true;
    }

    private void EventHandler3(object sender, ChainEventArgs e)
    {
        Console.WriteLine($"处理器3: {e.Data}");
    }
}
```

**Pattern 4:** 事件参数设计
```csharp
// 事件参数设计
public class CustomEventArgs : EventArgs
{
    // 自定义属性
    public string Message { get; }
    public int Value { get; }
    public bool Handled { get; set; }

    // 构造函数
    public CustomEventArgs(string message, int value)
    {
        Message = message;
        Value = value;
        Handled = false;
    }
}

// 泛型事件参数
public class GenericEventArgs<T> : EventArgs
{
    public T Data { get; }
    public bool Cancel { get; set; }

    public GenericEventArgs(T data)
    {
        Data = data;
        Cancel = false;
    }
}

// 使用自定义事件参数
public class EventPublisher
{
    // 定义使用自定义事件参数的事件
    public event EventHandler<CustomEventArgs> CustomEvent;
    public event EventHandler<GenericEventArgs<string>> GenericEvent;

    // 触发自定义事件
    public void RaiseCustomEvent(string message, int value)
    {
        var args = new CustomEventArgs(message, value);
        OnCustomEvent(args);
    }

    // 触发泛型事件
    public void RaiseGenericEvent(string data)
    {
        var args = new GenericEventArgs<string>(data);
        OnGenericEvent(args);
    }

    protected virtual void OnCustomEvent(CustomEventArgs e)
    {
        CustomEvent?.Invoke(this, e);
    }

    protected virtual void OnGenericEvent(GenericEventArgs<string> e)
    {
        GenericEvent?.Invoke(this, e);
    }
}

// 使用示例
public class MainForm : Form
{
    private EventPublisher _publisher;

    public MainForm()
    {
        InitializeComponent();
        
        _publisher = new EventPublisher();
        _publisher.CustomEvent += OnCustomEvent;
        _publisher.GenericEvent += OnGenericEvent;
    }

    private void OnCustomEvent(object sender, CustomEventArgs e)
    {
        Console.WriteLine($"自定义事件: {e.Message}, 值: {e.Value}");
        e.Handled = true;
    }

    private void OnGenericEvent(object sender, GenericEventArgs<string> e)
    {
        Console.WriteLine($"泛型事件: {e.Data}");
    }

    private void TriggerEvents(object sender, EventArgs e)
    {
        _publisher.RaiseCustomEvent("测试消息", 42);
        _publisher.RaiseGenericEvent("测试数据");
    }
}
```

**Pattern 5:** 控件事件继承
```csharp
// 控件事件继承
// 自定义按钮控件
public class CustomButton : Button
{
    // 定义自定义事件
    public event EventHandler<CustomClickEventArgs> CustomClick;

    // 重写基类的OnClick方法
    protected override void OnClick(EventArgs e)
    {
        // 先触发自定义事件
        var customArgs = new CustomClickEventArgs("自定义点击事件", DateTime.Now);
        OnCustomClick(customArgs);

        // 如果自定义事件没有被取消，再调用基类的OnClick
        if (!customArgs.Cancel)
        {
            base.OnClick(e);
        }
    }

    // 触发自定义事件的方法
    protected virtual void OnCustomClick(CustomClickEventArgs e)
    {
        CustomClick?.Invoke(this, e);
    }
}

// 自定义事件参数
public class CustomClickEventArgs : EventArgs
{
    public string Message { get; }
    public DateTime ClickTime { get; }
    public bool Cancel { get; set; }

    public CustomClickEventArgs(string message, DateTime clickTime)
    {
        Message = message;
        ClickTime = clickTime;
        Cancel = false;
    }
}

// 扩展现有控件事件
public class EnhancedTextBox : TextBox
{
    // 定义增强事件
    public event EventHandler<TextChangedEventArgs> EnhancedTextChanged;

    // 重写基类的OnTextChanged方法
    protected override void OnTextChanged(EventArgs e)
    {
        // 调用基类的方法
        base.OnTextChanged(e);

        // 触发增强事件
        var enhancedArgs = new TextChangedEventArgs(this.Text, this.Text.Length);
        OnEnhancedTextChanged(enhancedArgs);
    }

    protected virtual void OnEnhancedTextChanged(TextChangedEventArgs e)
    {
        EnhancedTextChanged?.Invoke(this, e);
    }
}

// 增强的文本变更事件参数
public class TextChangedEventArgs : EventArgs
{
    public string NewText { get; }
    public int TextLength { get; }

    public TextChangedEventArgs(string newText, int textLength)
    {
        NewText = newText;
        TextLength = textLength;
    }
}

// 使用示例
public class MainForm : Form
{
    private CustomButton _customButton;
    private EnhancedTextBox _enhancedTextBox;

    public MainForm()
    {
        InitializeComponent();
        InitializeCustomControls();
    }

    private void InitializeCustomControls()
    {
        _customButton = new CustomButton
        {
            Text = "自定义按钮",
            Location = new Point(10, 10),
            Size = new Size(120, 30)
        };

        _enhancedTextBox = new EnhancedTextBox
        {
            Location = new Point(10, 50),
            Size = new Size(200, 30)
        };

        Controls.Add(_customButton);
        Controls.Add(_enhancedTextBox);

        // 绑定事件
        _customButton.Click += CustomButton_Click;
        _customButton.CustomClick += CustomButton_CustomClick;
        _enhancedTextBox.EnhancedTextChanged += EnhancedTextBox_EnhancedTextChanged;
    }

    private void CustomButton_Click(object sender, EventArgs e)
    {
        Console.WriteLine("标准点击事件");
    }

    private void CustomButton_CustomClick(object sender, CustomClickEventArgs e)
    {
        Console.WriteLine($"自定义点击事件: {e.Message}, 时间: {e.ClickTime}");
        // 可以取消标准点击事件
        // e.Cancel = true;
    }

    private void EnhancedTextBox_EnhancedTextChanged(object sender, TextChangedEventArgs e)
    {
        Console.WriteLine($"增强文本变更: {e.NewText}, 长度: {e.TextLength}");
    }
}
```

## Examples

### Example 1: 管理事件绑定
- Input: 需要管理表单中的事件绑定
- Steps:
  1. 识别需要绑定事件的控件
  2. 使用适当的绑定方式（方法组、lambda、匿名方法）
  3. 实现事件处理程序
  4. 在适当的时候解绑事件
  5. 测试事件绑定效果
- Expected output / acceptance: 事件绑定正确实现和管理

### Example 2: 处理异步事件
- Input: 需要处理耗时操作的事件
- Steps:
  1. 将事件处理程序标记为async
  2. 使用await处理异步操作
  3. 处理异常情况
  4. 管理取消操作
  5. 测试异步事件处理
- Expected output / acceptance: 异步事件正确处理，UI响应良好

### Example 3: 设计事件参数
- Input: 需要为自定义事件设计参数
- Steps:
  1. 创建自定义事件参数类
  2. 添加必要的属性
  3. 实现事件发布和订阅
  4. 测试事件参数使用
- Expected output / acceptance: 事件参数正确设计和使用

## References

- `references/index.md`: 事件处理最佳实践导航
- `references/event-binding.md`: 事件绑定管理指南
- `references/async-events.md`: 异步事件处理指南
- `references/event-chain.md`: 事件链管理指南
- `references/event-args-design.md`: 事件参数设计指南
- `references/event-inheritance.md`: 控件事件继承指南

## Maintenance

- Sources: WinForms官方文档和事件处理最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现