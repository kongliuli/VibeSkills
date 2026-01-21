---
name: dialog-manager
description: "C# MVVM WPF 对话框管理器技能：负责窗口服务抽象、模态对话框管理、ViewModel间消息传递、对话框结果处理、窗口生命周期控制。使用时需要抽象窗口服务、管理模态对话框、处理ViewModel间消息传递、处理对话框结果、控制窗口生命周期。"
---

# dialog-manager Skill

为C# MVVM WPF项目提供专业的对话框管理和配置能力，确保对话框具有规范、一致、高效的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要抽象窗口服务
- 需要管理模态对话框
- 需要处理ViewModel间消息传递
- 需要处理对话框结果
- 需要控制窗口生命周期
- 需要优化现有对话框实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行对话框设计决策
- 不处理对话框的深度性能优化（仅设计层面）
- 不负责对话框的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** 窗口服务抽象
```csharp
// 窗口服务接口
public interface IWindowService
{
    void ShowWindow(object viewModel);
    bool? ShowDialog(object viewModel);
    void CloseWindow(object viewModel);
}

// 窗口服务实现
public class WindowService : IWindowService
{
    private readonly Dictionary<Type, Type> _viewModelToViewMapping;

    public WindowService()
    {
        _viewModelToViewMapping = new Dictionary<Type, Type>();
        // 注册ViewModel到View的映射
        RegisterMappings();
    }

    private void RegisterMappings()
    {
        // 示例映射
        _viewModelToViewMapping[typeof(UserViewModel)] = typeof(UserView);
        _viewModelToViewMapping[typeof(ProductViewModel)] = typeof(ProductView);
        _viewModelToViewMapping[typeof(DialogViewModel)] = typeof(DialogView);
    }

    public void ShowWindow(object viewModel)
    {
        var window = CreateWindow(viewModel);
        window.Show();
    }

    public bool? ShowDialog(object viewModel)
    {
        var window = CreateWindow(viewModel);
        return window.ShowDialog();
    }

    public void CloseWindow(object viewModel)
    {
        // 查找并关闭对应的窗口
        var windows = Application.Current.Windows.OfType<Window>();
        foreach (var window in windows)
        {
            if (window.DataContext == viewModel)
            {
                window.Close();
                break;
            }
        }
    }

    private Window CreateWindow(object viewModel)
    {
        var viewModelType = viewModel.GetType();
        if (!_viewModelToViewMapping.TryGetValue(viewModelType, out var viewType))
        {
            throw new InvalidOperationException($"No view registered for view model type: {viewModelType.Name}");
        }

        var window = (Window)Activator.CreateInstance(viewType);
        window.DataContext = viewModel;
        return window;
    }
}

// 在依赖注入中注册
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IWindowService, WindowService>();
}
```

**Pattern 2:** 模态对话框管理
```csharp
// 对话框ViewModel基类
public class DialogViewModelBase : ViewModelBase, IDialogResult
{
    private bool? _dialogResult;
    public bool? DialogResult
    {
        get { return _dialogResult; }
        set { SetProperty(ref _dialogResult, value); }
    }

    public ICommand OkCommand { get; }
    public ICommand CancelCommand { get; }

    public DialogViewModelBase()
    {
        OkCommand = new RelayCommand(Ok);
        CancelCommand = new RelayCommand(Cancel);
    }

    protected virtual void Ok()
    {
        DialogResult = true;
        Close();
    }

    protected virtual void Cancel()
    {
        DialogResult = false;
        Close();
    }

    protected void Close()
    {
        // 通过窗口服务关闭窗口
        var windowService = ServiceLocator.Current.GetInstance<IWindowService>();
        windowService.CloseWindow(this);
    }
}

// 使用对话框
public class MainViewModel : ViewModelBase
{
    private readonly IWindowService _windowService;

    public ICommand ShowDialogCommand { get; }

    public MainViewModel(IWindowService windowService)
    {
        _windowService = windowService;
        ShowDialogCommand = new RelayCommand(ShowDialog);
    }

    private void ShowDialog()
    {
        var dialogViewModel = new DialogViewModel();
        var result = _windowService.ShowDialog(dialogViewModel);

        if (result == true)
        {
            // 处理对话框确认结果
        }
        else
        {
            // 处理对话框取消结果
        }
    }
}
```

**Pattern 3:** ViewModel间消息传递
```csharp
// 消息服务接口
public interface IMessengerService
{
    void Register<TMessage>(object recipient, Action<TMessage> action);
    void Unregister<TMessage>(object recipient);
    void Send<TMessage>(TMessage message);
}

// 消息服务实现
public class MessengerService : IMessengerService
{
    private readonly Dictionary<Type, List<WeakAction>> _messageHandlers;

    public MessengerService()
    {
        _messageHandlers = new Dictionary<Type, List<WeakAction>>();
    }

    public void Register<TMessage>(object recipient, Action<TMessage> action)
    {
        var messageType = typeof(TMessage);
        if (!_messageHandlers.ContainsKey(messageType))
        {
            _messageHandlers[messageType] = new List<WeakAction>();
        }

        _messageHandlers[messageType].Add(new WeakAction(recipient, action));
    }

    public void Unregister<TMessage>(object recipient)
    {
        var messageType = typeof(TMessage);
        if (_messageHandlers.ContainsKey(messageType))
        {
            _messageHandlers[messageType].RemoveAll(wa => wa.Target == recipient);
        }
    }

    public void Send<TMessage>(TMessage message)
    {
        var messageType = typeof(TMessage);
        if (_messageHandlers.ContainsKey(messageType))
        {
            var handlers = _messageHandlers[messageType].ToList();
            foreach (var handler in handlers)
            {
                if (handler.IsAlive)
                {
                    ((Action<TMessage>)handler.Action).Invoke(message);
                }
                else
                {
                    _messageHandlers[messageType].Remove(handler);
                }
            }
        }
    }

    // 弱引用动作类
    private class WeakAction
    {
        public WeakReference Target { get; }
        public Delegate Action { get; }

        public WeakAction(object target, Delegate action)
        {
            Target = new WeakReference(target);
            Action = action;
        }

        public bool IsAlive => Target.IsAlive;
    }
}

// 消息类
public class UserUpdatedMessage
{
    public int UserId { get; set; }
    public string UserName { get; set; }
}

// 发送消息
public class UserViewModel : ViewModelBase
{
    private readonly IMessengerService _messengerService;

    public UserViewModel(IMessengerService messengerService)
    {
        _messengerService = messengerService;
    }

    private void UpdateUser()
    {
        // 更新用户逻辑
        
        // 发送消息
        _messengerService.Send(new UserUpdatedMessage
        {
            UserId = Id,
            UserName = Name
        });
    }
}

// 接收消息
public class MainViewModel : ViewModelBase
{
    private readonly IMessengerService _messengerService;

    public MainViewModel(IMessengerService messengerService)
    {
        _messengerService = messengerService;
        // 注册消息接收
        _messengerService.Register<UserUpdatedMessage>(this, OnUserUpdated);
    }

    private void OnUserUpdated(UserUpdatedMessage message)
    {
        // 处理用户更新消息
        Console.WriteLine($"User updated: {message.UserName} (ID: {message.UserId})");
        // 刷新用户列表等操作
    }
}
```

**Pattern 4:** 对话框结果处理
```csharp
// 对话框结果接口
public interface IDialogResult
{
    bool? DialogResult { get; set; }
}

// 确认对话框ViewModel
public class ConfirmationDialogViewModel : DialogViewModelBase
{
    private string _message;
    public string Message
    {
        get { return _message; }
        set { SetProperty(ref _message, value); }
    }

    private string _title;
    public string Title
    {
        get { return _title; }
        set { SetProperty(ref _title, value); }
    }

    public ConfirmationDialogViewModel(string message, string title = "Confirmation")
    {
        Message = message;
        Title = title;
    }
}

// 使用确认对话框
public class MainViewModel : ViewModelBase
{
    private readonly IWindowService _windowService;

    public ICommand DeleteCommand { get; }

    public MainViewModel(IWindowService windowService)
    {
        _windowService = windowService;
        DeleteCommand = new RelayCommand(Delete);
    }

    private void Delete()
    {
        var dialogViewModel = new ConfirmationDialogViewModel(
            "Are you sure you want to delete this item?", 
            "Delete Confirmation"
        );

        var result = _windowService.ShowDialog(dialogViewModel);

        if (result == true)
        {
            // 执行删除操作
        }
    }
}
```

**Pattern 5:** 窗口生命周期控制
```csharp
// 窗口生命周期接口
public interface IWindowLifecycle
{
    event EventHandler WindowLoaded;
    event EventHandler WindowClosing;
    event EventHandler WindowClosed;

    void OnLoaded();
    void OnClosing(CancelEventArgs e);
    void OnClosed();
}

// 窗口生命周期实现
public class WindowLifecycle : IWindowLifecycle
{
    public event EventHandler WindowLoaded;
    public event EventHandler WindowClosing;
    public event EventHandler WindowClosed;

    public void OnLoaded()
    {
        WindowLoaded?.Invoke(this, EventArgs.Empty);
    }

    public void OnClosing(CancelEventArgs e)
    {
        WindowClosing?.Invoke(this, e);
    }

    public void OnClosed()
    {
        WindowClosed?.Invoke(this, EventArgs.Empty);
    }
}

// 在窗口中使用
public partial class MainWindow : Window
{
    private readonly IWindowLifecycle _lifecycle;

    public MainWindow(IWindowLifecycle lifecycle)
    {
        InitializeComponent();
        _lifecycle = lifecycle;

        // 订阅窗口事件
        Loaded += MainWindow_Loaded;
        Closing += MainWindow_Closing;
        Closed += MainWindow_Closed;

        // 订阅生命周期事件
        _lifecycle.WindowClosing += Lifecycle_WindowClosing;
    }

    private void MainWindow_Loaded(object sender, RoutedEventArgs e)
    {
        _lifecycle.OnLoaded();
    }

    private void MainWindow_Closing(object sender, System.ComponentModel.CancelEventArgs e)
    {
        _lifecycle.OnClosing(e);
    }

    private void MainWindow_Closed(object sender, EventArgs e)
    {
        _lifecycle.OnClosed();
    }

    private void Lifecycle_WindowClosing(object sender, EventArgs e)
    {
        // 处理窗口关闭逻辑
        var cancelEventArgs = e as System.ComponentModel.CancelEventArgs;
        if (cancelEventArgs != null)
        {
            // 示例：确认关闭
            var result = MessageBox.Show("Are you sure you want to close?", "Confirm", MessageBoxButton.YesNo);
            if (result == MessageBoxResult.No)
            {
                cancelEventArgs.Cancel = true;
            }
        }
    }
}
```

## Examples

### Example 1: 抽象窗口服务
- Input: 需要创建窗口服务抽象
- Steps:
  1. 创建IWindowService接口
  2. 实现WindowService类
  3. 注册ViewModel到View的映射
  4. 在依赖注入中注册服务
  5. 测试窗口服务
- Expected output / acceptance: 窗口服务正确实现并可使用

### Example 2: 管理模态对话框
- Input: 需要创建和管理模态对话框
- Steps:
  1. 创建DialogViewModelBase类
  2. 实现Ok和Cancel命令
  3. 在WindowService中处理对话框显示
  4. 在MainViewModel中使用对话框
  5. 测试对话框功能
- Expected output / acceptance: 模态对话框正确显示和响应

### Example 3: ViewModel间消息传递
- Input: 需要实现ViewModel间消息传递
- Steps:
  1. 创建IMessengerService接口
  2. 实现MessengerService类
  3. 创建消息类
  4. 在ViewModel中发送和接收消息
  5. 测试消息传递功能
- Expected output / acceptance: ViewModel间消息正确传递

## References

- `references/index.md`: 对话框管理最佳实践导航
- `references/window-service.md`: 窗口服务抽象指南
- `references/dialog-management.md`: 模态对话框管理指南
- `references/messenger.md`: ViewModel间消息传递指南
- `references/dialog-result.md`: 对话框结果处理指南
- `references/window-lifecycle.md`: 窗口生命周期控制指南

## Maintenance

- Sources: WPF官方文档和MVVM模式最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现