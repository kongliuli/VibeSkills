# Avalonia窗口管理器 (window-manager)

为C# Avalonia项目提供专业的窗口管理和控制能力，确保应用能够正确管理窗口生命周期、处理多窗口通信、集成原生菜单、适配对话框和管理窗口状态。

## 主要功能

- **窗口生命周期控制**：管理窗口的打开、关闭、激活和停用等生命周期事件
- **多窗口通信**：实现主窗口与子窗口之间的数据传递和通信
- **Native菜单集成**：集成平台原生菜单系统，提供一致的用户体验
- **对话框适配**：适配和管理各种类型的对话框，包括消息框、文件选择框等
- **窗口状态管理**：保存和恢复窗口状态，如位置、大小和最大化状态

## 使用场景

- 需要控制窗口生命周期
- 需要管理多窗口通信
- 需要集成Native菜单
- 需要适配对话框
- 需要管理窗口状态
- 需要优化现有窗口管理实现

## 快速开始

### 窗口生命周期控制
```csharp
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
    }
}
```

### 多窗口通信
```csharp
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
```

### Native菜单集成
```csharp
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
        
        // 添加到主菜单
        menu.Items.Add(fileMenu);
        
        // 设置窗口菜单
        this.Menu = menu;
    }

    private void OnNewItemClick(object sender, RoutedEventArgs e)
    {
        Console.WriteLine("新建");
    }

    private void OnExitItemClick(object sender, RoutedEventArgs e)
    {
        Close();
    }
}
```

### 对话框适配
```csharp
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
}
```

### 窗口状态管理
```csharp
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

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件