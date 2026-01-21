# ViewModel生成器 (viewmodel-generator)

为C# MVVM WPF项目提供专业的ViewModel生成和配置能力，确保ViewModel具有规范、一致、高效的实现。

## 主要功能

- **INotifyPropertyChanged实现**：实现属性变更通知接口
- **ObservableCollection管理**：管理和操作可观察集合
- **RelayCommand生成**：生成和配置命令对象
- **属性变更通知**：处理属性变更和依赖属性更新
- **ViewModel基类设计**：设计和实现ViewModel基类

## 使用场景

- 需要实现INotifyPropertyChanged
- 需要管理ObservableCollection
- 需要生成RelayCommand
- 需要处理属性变更通知
- 需要设计ViewModel基类
- 需要优化现有ViewModel实现

## 快速开始

### ViewModel基类设计
```csharp
// ViewModel基类
public class ViewModelBase : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    protected bool SetProperty<T>(ref T field, T newValue, [CallerMemberName] string propertyName = null)
    {
        if (EqualityComparer<T>.Default.Equals(field, newValue))
            return false;

        field = newValue;
        OnPropertyChanged(propertyName);
        return true;
    }
}
```

### RelayCommand实现
```csharp
// RelayCommand实现
public class RelayCommand : ICommand
{
    private readonly Action _execute;
    private readonly Func<bool> _canExecute;

    public event EventHandler CanExecuteChanged;

    public RelayCommand(Action execute, Func<bool> canExecute = null)
    {
        _execute = execute ?? throw new ArgumentNullException(nameof(execute));
        _canExecute = canExecute;
    }

    public bool CanExecute(object parameter)
    {
        return _canExecute == null || _canExecute();
    }

    public void Execute(object parameter)
    {
        _execute();
    }

    public void RaiseCanExecuteChanged()
    {
        CanExecuteChanged?.Invoke(this, EventArgs.Empty);
    }
}
```

### ObservableCollection管理
```csharp
// ObservableCollection管理
public class UsersViewModel : ViewModelBase
{
    private ObservableCollection<User> _users;
    public ObservableCollection<User> Users
    {
        get { return _users; }
        set { SetProperty(ref _users, value); }
    }

    private User _selectedUser;
    public User SelectedUser
    {
        get { return _selectedUser; }
        set { SetProperty(ref _selectedUser, value); }
    }

    public ICommand AddUserCommand { get; }
    public ICommand RemoveUserCommand { get; }

    public UsersViewModel()
    {
        Users = new ObservableCollection<User>();
        AddUserCommand = new RelayCommand(AddUser);
        RemoveUserCommand = new RelayCommand(RemoveUser, CanRemoveUser);
    }

    private void AddUser()
    {
        var newUser = new User { Name = "New User" };
        Users.Add(newUser);
        SelectedUser = newUser;
    }

    private void RemoveUser()
    {
        if (SelectedUser != null)
        {
            Users.Remove(SelectedUser);
            SelectedUser = Users.FirstOrDefault();
        }
    }

    private bool CanRemoveUser()
    {
        return SelectedUser != null;
    }
}
```

### 完整ViewModel示例
```csharp
// 完整ViewModel示例
public class ProductViewModel : ViewModelBase
{
    private string _name;
    public string Name
    {
        get { return _name; }
        set { SetProperty(ref _name, value); }
    }

    private decimal _price;
    public decimal Price
    {
        get { return _price; }
        set { SetProperty(ref _price, value); }
    }

    private bool _isAvailable;
    public bool IsAvailable
    {
        get { return _isAvailable; }
        set { SetProperty(ref _isAvailable, value); }
    }

    public ICommand SaveCommand { get; }
    public ICommand CancelCommand { get; }

    public ProductViewModel()
    {
        SaveCommand = new RelayCommand(Save, CanSave);
        CancelCommand = new RelayCommand(Cancel);
    }

    private void Save()
    {
        // 保存逻辑
    }

    private bool CanSave()
    {
        return !string.IsNullOrEmpty(Name) && Price > 0;
    }

    private void Cancel()
    {
        // 取消逻辑
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件