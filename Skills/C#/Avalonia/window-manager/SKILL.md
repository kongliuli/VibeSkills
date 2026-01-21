---
name: window-manager
description: "C# Avalonia窗口管理器技能：负责窗口生命周期控制、多窗口通信、Native菜单集成、对话框适配、窗口状态管理。使用时需要控制窗口生命周期、管理多窗口通信、集成Native菜单、适配对话框、管理窗口状态。"
---

# window-manager Skill

为C# Avalonia项目提供专业的窗口管理和控制能力，确保应用能够正确管理窗口生命周期、处理多窗口通信、集成原生菜单、适配对话框和管理窗口状态。

## When to Use This Skill

Trigger when any of these applies:
- 需要控制窗口生命周期
- 需要管理多窗口通信
- 需要集成Native菜单
- 需要适配对话框
- 需要管理窗口状态
- 需要优化现有窗口管理实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行窗口设计决策
- 不处理窗口的深度性能优化（仅设计层面）
- 不负责窗口的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** 窗口生命周期控制
```csharp
// 窗口生命周期控制
public class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        
        // 注册生命周期事件
        Opened += OnWindowOpened;
        Closing += OnWindowClosing;
        Closed += OnWindowClosed;
        Activated += OnWindowActivated;
        Deactivated += OnWindowDeactivated;
    }

    private void OnWindowOpened(object sender, EventArgs e)
    {
        // 窗口打开时执行
        Console.WriteLine("Window opened");
        // 加载初始数据
        LoadInitialData();
    }

    private void OnWindowClosing(object sender, WindowClosingEventArgs e)
    {
        // 窗口关闭前执行
        Console.WriteLine("Window closing");
        
        // 询问用户是否确认关闭
        var result = MessageBox.Show(this, "确定要关闭窗口吗？", "确认", MessageBoxButton.YesNo);
        if (result == MessageBoxResult.No)
        {
            e.Cancel = true; // 取消关闭
        }
        else
        {
            // 保存数据
            SaveData();
        }
    }

    private void OnWindowClosed(object sender, EventArgs e)
    {
        // 窗口关闭后执行
        Console.WriteLine("Window closed");
        // 释放资源
        DisposeResources();
    }

    private void OnWindowActivated(object sender, EventArgs e)
    {
        // 窗口激活时执行
        Console.WriteLine("Window activated");
    }

    private void OnWindowDeactivated(object sender, EventArgs e)
    {
        // 窗口失去激活时执行
        Console.WriteLine("Window deactivated");
    }

    private void LoadInitialData()
    {
        // 加载初始数据的实现
    }

    private void SaveData()
    {
        // 保存数据的实现
    }

    private void DisposeResources()
    {
        // 释放资源的实现
    }
}
```

**Pattern 2:** 多窗口通信
```csharp
// 多窗口通信
// 方法1：使用事件
public class MainWindow : Window
{
    private ChildWindow _childWindow;

    public MainWindow()
    {
        InitializeComponent();
        
        // 创建子窗口
        _childWindow = new ChildWindow();
        
        // 订阅子窗口事件
        _childWindow.DataChanged += OnChildWindowDataChanged;
    }

    private void OpenChildWindow(object sender, RoutedEventArgs e)
    {
        _childWindow.Show();
    }

    private void OnChildWindowDataChanged(object sender, DataChangedEventArgs e)
    {
        // 处理子窗口发送的数据
        Console.WriteLine($"Received data from child window: {e.Data}");
        // 更新主窗口UI
        UpdateMainWindowUI(e.Data);
    }

    private void UpdateMainWindowUI(string data)
    {
        // 更新主窗口UI的实现
    }
}

public class ChildWindow : Window
{
    // 定义事件
    public event EventHandler<DataChangedEventArgs> DataChanged;

    public ChildWindow()
    {
        InitializeComponent();
    }

    private void SendDataToMainWindow(object sender, RoutedEventArgs e)
    {
        string data = "Hello from child window";
        // 触发事件
        DataChanged?.Invoke(this, new DataChangedEventArgs(data));
    }
}

public class DataChangedEventArgs : EventArgs
{
    public string Data { get; }

    public DataChangedEventArgs(string data)
    {
        Data = data;
    }
}

// 方法2：使用依赖注入
public class WindowManagerService
{
    private List<Window> _openWindows = new List<Window>();

    public void RegisterWindow(Window window)
    {
        if (!_openWindows.Contains(window))
        {
            _openWindows.Add(window);
            window.Closed += (s, e) => _openWindows.Remove(window);
        }
    }

    public void SendMessageToAllWindows(string message)
    {
        foreach (var window in _openWindows)
        {
            if (window is IMessageReceiver messageReceiver)
            {
                messageReceiver.ReceiveMessage(message);
            }
        }
    }

    public void SendMessageToWindow<T>(string message) where T : Window
    {
        var window = _openWindows.FirstOrDefault(w => w is T);
        if (window is IMessageReceiver messageReceiver)
        {
            messageReceiver.ReceiveMessage(message);
        }
    }
}

public interface IMessageReceiver
{
    void ReceiveMessage(string message);
}

public class MainWindow : Window, IMessageReceiver
{
    public void ReceiveMessage(string message)
    {
        Console.WriteLine($"MainWindow received message: {message}");
    }
}

public class ChildWindow : Window, IMessageReceiver
{
    public void ReceiveMessage(string message)
    {
        Console.WriteLine($"ChildWindow received message: {message}");
    }
}
```

**Pattern 3:** Native菜单集成
```csharp
// Native菜单集成
public class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        
        // 创建菜单
        var menu = new NativeMenu();
        
        // 文件菜单
        var fileMenu = new NativeMenuItem("文件");
        var newItem = new NativeMenuItem("新建") { Gesture = KeyGesture.Parse("Ctrl+N") };
        var openItem = new NativeMenuItem("打开") { Gesture = KeyGesture.Parse("Ctrl+O") };
        var saveItem = new NativeMenuItem("保存") { Gesture = KeyGesture.Parse("Ctrl+S") };
        var exitItem = new NativeMenuItem("退出");
        
        newItem.Click += OnNewItemClick;
        openItem.Click += OnOpenItemClick;
        saveItem.Click += OnSaveItemClick;
        exitItem.Click += OnExitItemClick;
        
        fileMenu.Items.Add(newItem);
        fileMenu.Items.Add(openItem);
        fileMenu.Items.Add(saveItem);
        fileMenu.Items.Add(new NativeMenuItemSeparator());
        fileMenu.Items.Add(exitItem);
        
        // 编辑菜单
        var editMenu = new NativeMenuItem("编辑");
        var cutItem = new NativeMenuItem("剪切") { Gesture = KeyGesture.Parse("Ctrl+X") };
        var copyItem = new NativeMenuItem("复制") { Gesture = KeyGesture.Parse("Ctrl+C") };
        var pasteItem = new NativeMenuItem("粘贴") { Gesture = KeyGesture.Parse("Ctrl+V") };
        
        cutItem.Click += OnCutItemClick;
        copyItem.Click += OnCopyItemClick;
        pasteItem.Click += OnPasteItemClick;
        
        editMenu.Items.Add(cutItem);
        editMenu.Items.Add(copyItem);
        editMenu.Items.Add(pasteItem);
        
        // 帮助菜单
        var helpMenu = new NativeMenuItem("帮助");
        var aboutItem = new NativeMenuItem("关于");
        
        aboutItem.Click += OnAboutItemClick;
        
        helpMenu.Items.Add(aboutItem);
        
        // 添加到主菜单
        menu.Items.Add(fileMenu);
        menu.Items.Add(editMenu);
        menu.Items.Add(helpMenu);
        
        // 设置窗口菜单
        this.Menu = menu;
    }

    private void OnNewItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("新建");
    }

    private void OnOpenItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("打开");
    }

    private void OnSaveItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("保存");
    }

    private void OnExitItemClick(object sender, RoutedEventArgs e)
    {
        Close();
    }

    private void OnCutItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("剪切");
    }

    private void OnCopyItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("复制");
    }

    private void OnPasteItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("粘贴");
    }

    private void OnAboutItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("关于");
    }
}
```

**Pattern 4:** 对话框适配
```csharp
// 对话框适配
public class DialogService
{
    public async Task<MessageBoxResult> ShowMessageBoxAsync(Window owner, string message, string title, MessageBoxButton buttons)
    {
        return await MessageBox.Show(owner, message, title, buttons);
    }

    public async Task<string> ShowOpenFileDialogAsync(Window owner, string filter = "All files (*.*)|*.*")
    {
        var dialog = new OpenFileDialog
        {
            Filters = {
                new FileDialogFilter { Name = "All files", Extensions = { "*" } }
            },
            AllowMultiple = false
        };

        var result = await dialog.ShowAsync(owner);
        return result?.FirstOrDefault();
    }

    public async Task<string> ShowSaveFileDialogAsync(Window owner, string defaultFileName = "", string filter = "All files (*.*)|*.*")
    {
        var dialog = new SaveFileDialog
        {
            InitialFileName = defaultFileName,
            Filters = {
                new FileDialogFilter { Name = "All files", Extensions = { "*" } }
            }
        };

        return await dialog.ShowAsync(owner);
    }

    public async Task<string> ShowFolderBrowserDialogAsync(Window owner, string title = "选择文件夹")
    {
        var dialog = new OpenFolderDialog
        {
            Title = title
        };

        return await dialog.ShowAsync(owner);
    }

    public async Task<TResult> ShowCustomDialogAsync<TResult>(Window owner, Window dialogWindow)
    {
        // 设置对话框属性
        dialogWindow.WindowStartupLocation = WindowStartupLocation.CenterOwner;
        dialogWindow.Owner = owner;
        dialogWindow.CanResize = false;
        
        // 显示对话框并等待结果
        var result = await dialogWindow.ShowDialog<TResult>(owner);
        return result;
    }
}

// 使用示例
public class MainWindow : Window
{
    private readonly DialogService _dialogService;

    public MainWindow()
    {
        InitializeComponent();
        _dialogService = new DialogService();
    }

    private async void ShowMessageBox(object sender, RoutedEventArgs e)
    {
        var result = await _dialogService.ShowMessageBoxAsync(this, "这是一个消息框", "提示", MessageBoxButton.YesNoCancel);
        Console.WriteLine($"MessageBox result: {result}");
    }

    private async void OpenFile(object sender, RoutedEventArgs e)
    {
        var filePath = await _dialogService.ShowOpenFileDialogAsync(this, "Text files (*.txt)|*.txt|All files (*.*)|*.*");
        if (!string.IsNullOrEmpty(filePath))
        {
            Console.WriteLine($"Selected file: {filePath}");
            // 处理文件
        }
    }

    private async void SaveFile(object sender, RoutedEventArgs e)
    {
        var filePath = await _dialogService.ShowSaveFileDialogAsync(this, "document.txt", "Text files (*.txt)|*.txt|All files (*.*)|*.*");
        if (!string.IsNullOrEmpty(filePath))
        {
            Console.WriteLine($"Save file to: {filePath}");
            // 保存文件
        }
    }

    private async void SelectFolder(object sender, RoutedEventArgs e)
    {
        var folderPath = await _dialogService.ShowFolderBrowserDialogAsync(this, "选择保存位置");
        if (!string.IsNullOrEmpty(folderPath))
        {
            Console.WriteLine($"Selected folder: {folderPath}");
            // 处理文件夹
        }
    }

    private async void ShowCustomDialog(object sender, RoutedEventArgs e)
    {
        var customDialog = new CustomDialogWindow();
        var result = await _dialogService.ShowCustomDialogAsync<string>(this, customDialog);
        if (!string.IsNullOrEmpty(result))
        {
            Console.WriteLine($"Custom dialog result: {result}");
            // 处理结果
        }
    }
}
```

**Pattern 5:** 窗口状态管理
```csharp
// 窗口状态管理
public class WindowStateManager
{
    private const string WindowStateFileName = "windowState.json";

    public void SaveWindowState(Window window)
    {
        var state = new WindowState
        {
            Width = window.Width,
            Height = window.Height,
            X = window.Position.X,
            Y = window.Position.Y,
            WindowState = window.WindowState
        };

        var json = JsonSerializer.Serialize(state, new JsonSerializerOptions { WriteIndented = true });
        var filePath = GetWindowStateFilePath();
        File.WriteAllText(filePath, json);
    }

    public void LoadWindowState(Window window)
    {
        var filePath = GetWindowStateFilePath();
        if (File.Exists(filePath))
        {
            try
            {
                var json = File.ReadAllText(filePath);
                var state = JsonSerializer.Deserialize<WindowState>(json);
                
                if (state != null)
                {
                    window.Width = state.Width;
                    window.Height = state.Height;
                    window.Position = new PixelPoint((int)state.X, (int)state.Y);
                    window.WindowState = state.WindowState;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error loading window state: {ex.Message}");
            }
        }
    }

    private string GetWindowStateFilePath()
    {
        var appDataPath = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
        var appPath = Path.Combine(appDataPath, "MyApp");
        Directory.CreateDirectory(appPath);
        return Path.Combine(appPath, WindowStateFileName);
    }
}

public class WindowState
{
    public double Width { get; set; } = 800;
    public double Height { get; set; } = 600;
    public double X { get; set; } = 100;
    public double Y { get; set; } = 100;
    public WindowState WindowState { get; set; } = WindowState.Normal;
}

// 使用示例
public class MainWindow : Window
{
    private readonly WindowStateManager _windowStateManager;

    public MainWindow()
    {
        InitializeComponent();
        _windowStateManager = new WindowStateManager();
        
        // 加载窗口状态
        _windowStateManager.LoadWindowState(this);
        
        // 注册关闭事件以保存状态
        Closing += (s, e) => _windowStateManager.SaveWindowState(this);
    }
}
```

## Examples

### Example 1: 控制窗口生命周期
- Input: 需要控制窗口生命周期
- Steps:
  1. 注册窗口生命周期事件
  2. 实现事件处理方法
  3. 测试生命周期控制
- Expected output / acceptance: 窗口生命周期事件正确触发和处理

### Example 2: 管理多窗口通信
- Input: 需要管理多窗口通信
- Steps:
  1. 选择通信方式（事件、依赖注入等）
  2. 实现通信逻辑
  3. 测试多窗口通信
- Expected output / acceptance: 多窗口通信正确实现和测试

### Example 3: 集成Native菜单
- Input: 需要集成Native菜单
- Steps:
  1. 创建Native菜单
  2. 添加菜单项和事件处理
  3. 测试菜单功能
- Expected output / acceptance: Native菜单正确集成和显示

## References

- `references/index.md`: 窗口管理最佳实践导航
- `references/window-lifecycle.md`: 窗口生命周期控制指南
- `references/multi-window-communication.md`: 多窗口通信指南
- `references/native-menu-integration.md`: Native菜单集成指南
- `references/dialog-adaptation.md`: 对话框适配指南
- `references/window-state-management.md`: 窗口状态管理指南

## Maintenance

- Sources: Avalonia官方文档和窗口管理最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现