# WinForms事件处理器 (event-handler)

为C# WinForms项目提供专业的事件处理和管理能力，确保应用能够正确处理各种事件场景，包括事件绑定、异步处理、事件链管理、参数设计和事件继承。

## 主要功能

- **事件绑定管理**：管理控件事件的绑定和解绑，支持多种绑定方式
- **异步事件处理**：使用async/await处理耗时操作，确保UI响应流畅
- **事件链管理**：管理多个事件处理程序形成的事件链，支持事件取消
- **事件参数设计**：设计自定义事件参数，支持泛型和复杂数据传递
- **控件事件继承**：扩展现有控件的事件系统，添加自定义事件

## 使用场景

- 需要管理事件绑定
- 需要处理异步事件
- 需要管理事件链
- 需要设计事件参数
- 需要实现控件事件继承
- 需要优化现有事件处理实现

## 快速开始

### 事件绑定管理
```csharp
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
    }

    private void Button_Click(object sender, EventArgs e)
    {
        Console.WriteLine("按钮被点击");
        _textBox.Text = "按钮被点击";
    }
}
```

### 异步事件处理
```csharp
public class MainForm : Form
{
    private Button _asyncButton;
    private Label _statusLabel;

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
            // 执行异步操作
            string result = await PerformAsyncOperation();
            
            // 更新UI
            _statusLabel.Text = $"完成: {result}";
        }
        catch (Exception ex)
        {
            _statusLabel.Text = $"错误: {ex.Message}";
        }
        finally
        {
            // 重新启用按钮
            _asyncButton.Enabled = true;
        }
    }

    private async Task<string> PerformAsyncOperation()
    {
        // 模拟长时间运行的操作
        await Task.Delay(3000);
        return "异步操作完成";
    }
}
```

### 事件链管理
```csharp
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
    public string Data { get; set; }
    public bool Canceled { get; set; }

    public ChainEventArgs(string data)
    {
        Data = data;
        Canceled = false;
    }
}
```

### 事件参数设计
```csharp
// 自定义事件参数
public class CustomEventArgs : EventArgs
{
    public string Message { get; }
    public int Value { get; }
    public bool Handled { get; set; }

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
    public event EventHandler<CustomEventArgs> CustomEvent;
    public event EventHandler<GenericEventArgs<string>> GenericEvent;

    public void RaiseCustomEvent(string message, int value)
    {
        var args = new CustomEventArgs(message, value);
        CustomEvent?.Invoke(this, args);
    }

    public void RaiseGenericEvent(string data)
    {
        var args = new GenericEventArgs<string>(data);
        GenericEvent?.Invoke(this, args);
    }
}
```

### 控件事件继承
```csharp
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
        CustomClick?.Invoke(this, customArgs);

        // 如果自定义事件没有被取消，再调用基类的OnClick
        if (!customArgs.Cancel)
        {
            base.OnClick(e);
        }
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
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件