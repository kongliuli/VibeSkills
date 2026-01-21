# 对话框管理器 (dialog-manager)

为C# MVVM WPF项目提供专业的对话框管理和配置能力，确保对话框具有规范、一致、高效的实现。

## 主要功能

- **窗口服务抽象**：抽象和管理窗口服务
- **模态对话框管理**：管理和显示模态对话框
- **ViewModel间消息传递**：实现ViewModel之间的消息传递
- **对话框结果处理**：处理对话框的返回结果
- **窗口生命周期控制**：控制和管理窗口的生命周期

## 使用场景

- 需要抽象窗口服务
- 需要管理模态对话框
- 需要处理ViewModel间消息传递
- 需要处理对话框结果
- 需要控制窗口生命周期
- 需要优化现有对话框实现

## 快速开始

### 窗口服务抽象
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
```

### 模态对话框管理
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
```

### ViewModel间消息传递
```csharp
// 消息服务接口
public interface IMessengerService
{
    void Register<TMessage>(object recipient, Action<TMessage> action);
    void Unregister<TMessage>(object recipient);
    void Send<TMessage>(TMessage message);
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
        _messengerService.Register<UserUpdatedMessage>(this, OnUserUpdated);
    }

    private void OnUserUpdated(UserUpdatedMessage message)
    {
        // 处理用户更新消息
    }
}
```

### 对话框结果处理
```csharp
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

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件