# 异步命令专家 (async-command-expert)

为C# MVVM WPF项目提供专业的异步命令开发和配置能力，确保异步命令具有规范、一致、高效的实现。

## 主要功能

- **IAsyncRelayCommand实现**：实现和配置异步命令
- **异步任务取消**：处理和管理异步任务取消
- **进度报告**：实现和显示异步操作进度
- **命令可执行状态管理**：管理命令的可执行状态
- **异常处理**：处理异步操作中的异常

## 使用场景

- 需要实现IAsyncRelayCommand
- 需要处理异步任务取消
- 需要实现进度报告
- 需要管理命令可执行状态
- 需要处理异常
- 需要优化现有异步命令实现

## 快速开始

### IAsyncRelayCommand实现
```csharp
// IAsyncRelayCommand接口
public interface IAsyncRelayCommand : ICommand
{
    Task ExecuteAsync(object parameter);
    bool CanExecute(object parameter);
    void NotifyCanExecuteChanged();
}

// AsyncRelayCommand实现
public class AsyncRelayCommand : IAsyncRelayCommand
{
    private readonly Func<Task> _execute;
    private readonly Func<bool> _canExecute;
    private bool _isExecuting;

    public event EventHandler CanExecuteChanged;

    public bool IsExecuting
    {
        get { return _isExecuting; }
        private set
        {
            _isExecuting = value;
            NotifyCanExecuteChanged();
        }
    }

    public AsyncRelayCommand(Func<Task> execute, Func<bool> canExecute = null)
    {
        _execute = execute ?? throw new ArgumentNullException(nameof(execute));
        _canExecute = canExecute;
    }

    public bool CanExecute(object parameter)
    {
        return !IsExecuting && (_canExecute == null || _canExecute());
    }

    public async void Execute(object parameter)
    {
        await ExecuteAsync(parameter);
    }

    public async Task ExecuteAsync(object parameter)
    {
        if (CanExecute(parameter))
        {
            try
            {
                IsExecuting = true;
                await _execute();
            }
            finally
            {
                IsExecuting = false;
            }
        }
    }

    public void NotifyCanExecuteChanged()
    {
        CanExecuteChanged?.Invoke(this, EventArgs.Empty);
    }
}
```

### 异步任务取消
```csharp
// 带取消支持的AsyncRelayCommand
public class AsyncRelayCommandWithCancellation : IAsyncRelayCommand
{
    private readonly Func<CancellationToken, Task> _execute;
    private readonly Func<bool> _canExecute;
    private bool _isExecuting;
    private CancellationTokenSource _cts;

    public event EventHandler CanExecuteChanged;

    public bool IsExecuting
    {
        get { return _isExecuting; }
        private set
        {
            _isExecuting = value;
            NotifyCanExecuteChanged();
        }
    }

    public AsyncRelayCommandWithCancellation(Func<CancellationToken, Task> execute, Func<bool> canExecute = null)
    {
        _execute = execute ?? throw new ArgumentNullException(nameof(execute));
        _canExecute = canExecute;
    }

    public async Task ExecuteAsync(object parameter)
    {
        if (CanExecute(parameter))
        {
            try
            {
                IsExecuting = true;
                _cts = new CancellationTokenSource();
                await _execute(_cts.Token);
            }
            finally
            {
                IsExecuting = false;
                _cts?.Dispose();
                _cts = null;
            }
        }
    }

    public void Cancel()
    {
        _cts?.Cancel();
    }

    // 其他方法实现...
}
```

### 进度报告
```csharp
// 使用带进度报告的命令
public class MainViewModel : ViewModelBase
{
    public IAsyncRelayCommand DownloadCommand { get; }

    public MainViewModel()
    {
        DownloadCommand = new AsyncRelayCommandWithProgress(DownloadFile);
    }

    private async Task DownloadFile(IProgress<int> progress)
    {
        // 模拟文件下载
        for (int i = 0; i <= 100; i += 10)
        {
            await Task.Delay(500);
            progress?.Report(i);
            DownloadProgress = i;
        }
    }

    private int _downloadProgress;
    public int DownloadProgress
    {
        get { return _downloadProgress; }
        set { SetProperty(ref _downloadProgress, value); }
    }
}
```

### 命令可执行状态管理
```csharp
// 使用命令可执行状态
public class MainViewModel : ViewModelBase
{
    public IAsyncRelayCommand SaveCommand { get; }

    public MainViewModel()
    {
        SaveCommand = new AsyncRelayCommand(SaveData, CanSave);
    }

    private async Task SaveData()
    {
        // 保存数据逻辑
        await Task.Delay(1000);
        IsSaved = true;
    }

    private bool CanSave()
    {
        return !string.IsNullOrEmpty(Name) && !string.IsNullOrEmpty(Description);
    }

    private string _name;
    public string Name
    {
        get { return _name; }
        set 
        {
            if (SetProperty(ref _name, value))
            {
                ((AsyncRelayCommand)SaveCommand).NotifyCanExecuteChanged();
            }
        }
    }

    // 其他属性...
}
```

### 异常处理
```csharp
// 使用异常处理
public class MainViewModel : ViewModelBase
{
    public IAsyncRelayCommand RiskyCommand { get; }

    public MainViewModel()
    {
        RiskyCommand = new AsyncRelayCommand(ExecuteRiskyOperation);
    }

    private async Task ExecuteRiskyOperation()
    {
        try
        {
            // 可能抛出异常的操作
            await Task.Delay(1000);
            throw new InvalidOperationException("Something went wrong!");
        }
        catch (Exception ex)
        {
            // 处理异常
            ErrorMessage = ex.Message;
            IsError = true;
        }
    }

    private string _errorMessage;
    public string ErrorMessage
    {
        get { return _errorMessage; }
        set { SetProperty(ref _errorMessage, value); }
    }

    private bool _isError;
    public bool IsError
    {
        get { return _isError; }
        set { SetProperty(ref _isError, value); }
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件