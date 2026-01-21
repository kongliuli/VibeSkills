---
name: winforms-data-binding-expert
description: "C# WinForms数据绑定专家技能：负责BindingSource配置、DataGridView数据绑定、双向绑定模式、数据验证、绑定异常处理。使用时需要配置BindingSource、实现DataGridView数据绑定、使用双向绑定模式、进行数据验证、处理绑定异常。"
---

# winforms-data-binding-expert Skill

为C# WinForms项目提供专业的数据绑定和管理能力，确保应用能够正确处理数据绑定、显示和验证，提供流畅的用户体验。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置BindingSource
- 需要实现DataGridView数据绑定
- 需要使用双向绑定模式
- 需要进行数据验证
- 需要处理绑定异常
- 需要优化现有数据绑定实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行数据设计决策
- 不处理数据的深度性能优化（仅设计层面）
- 不负责数据的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** BindingSource配置
```csharp
// BindingSource配置
public class MainForm : Form
{
    private BindingSource _bindingSource;
    private List<Customer> _customers;
    private TextBox _nameTextBox;
    private TextBox _emailTextBox;
    private Button _nextButton;
    private Button _previousButton;

    public MainForm()
    {
        InitializeComponent();
        InitializeData();
        InitializeBindingSource();
        InitializeControls();
    }

    private void InitializeData()
    {
        // 初始化数据
        _customers = new List<Customer>
        {
            new Customer { Id = 1, Name = "张三", Email = "zhangsan@example.com" },
            new Customer { Id = 2, Name = "李四", Email = "lisi@example.com" },
            new Customer { Id = 3, Name = "王五", Email = "wangwu@example.com" }
        };
    }

    private void InitializeBindingSource()
    {
        // 创建和配置BindingSource
        _bindingSource = new BindingSource
        {
            DataSource = _customers
        };

        // 订阅BindingSource事件
        _bindingSource.CurrentChanged += BindingSource_CurrentChanged;
        _bindingSource.PositionChanged += BindingSource_PositionChanged;
    }

    private void InitializeControls()
    {
        // 创建控件
        _nameTextBox = new TextBox
        {
            Location = new Point(100, 20),
            Size = new Size(200, 20)
        };

        _emailTextBox = new TextBox
        {
            Location = new Point(100, 50),
            Size = new Size(200, 20)
        };

        _previousButton = new Button
        {
            Text = "上一个",
            Location = new Point(100, 80),
            Size = new Size(80, 30)
        };

        _nextButton = new Button
        {
            Text = "下一个",
            Location = new Point(190, 80),
            Size = new Size(80, 30)
        };

        var nameLabel = new Label
        {
            Text = "姓名:",
            Location = new Point(20, 20),
            Size = new Size(80, 20)
        };

        var emailLabel = new Label
        {
            Text = "邮箱:",
            Location = new Point(20, 50),
            Size = new Size(80, 20)
        };

        // 添加控件到表单
        Controls.Add(nameLabel);
        Controls.Add(_nameTextBox);
        Controls.Add(emailLabel);
        Controls.Add(_emailTextBox);
        Controls.Add(_previousButton);
        Controls.Add(_nextButton);

        // 绑定控件到BindingSource
        _nameTextBox.DataBindings.Add("Text", _bindingSource, "Name", true, DataSourceUpdateMode.OnPropertyChanged);
        _emailTextBox.DataBindings.Add("Text", _bindingSource, "Email", true, DataSourceUpdateMode.OnPropertyChanged);

        // 绑定按钮事件
        _previousButton.Click += PreviousButton_Click;
        _nextButton.Click += NextButton_Click;
    }

    private void BindingSource_CurrentChanged(object sender, EventArgs e)
    {
        // 当前项变更时的处理
        Console.WriteLine($"当前项变更: {((Customer)_bindingSource.Current).Name}");
    }

    private void BindingSource_PositionChanged(object sender, EventArgs e)
    {
        // 位置变更时的处理
        Console.WriteLine($"位置变更: {_bindingSource.Position}");
    }

    private void PreviousButton_Click(object sender, EventArgs e)
    {
        if (_bindingSource.Position > 0)
        {
            _bindingSource.Position--;
        }
    }

    private void NextButton_Click(object sender, EventArgs e)
    {
        if (_bindingSource.Position < _bindingSource.Count - 1)
        {
            _bindingSource.Position++;
        }
    }
}

// 数据模型类
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
}
```

**Pattern 2:** DataGridView数据绑定
```csharp
// DataGridView数据绑定
public class MainForm : Form
{
    private DataGridView _dataGridView;
    private BindingSource _bindingSource;
    private List<Customer> _customers;
    private Button _addButton;
    private Button _deleteButton;
    private Button _saveButton;

    public MainForm()
    {
        InitializeComponent();
        InitializeData();
        InitializeBindingSource();
        InitializeDataGridView();
        InitializeControls();
    }

    private void InitializeData()
    {
        // 初始化数据
        _customers = new List<Customer>
        {
            new Customer { Id = 1, Name = "张三", Email = "zhangsan@example.com", Age = 25 },
            new Customer { Id = 2, Name = "李四", Email = "lisi@example.com", Age = 30 },
            new Customer { Id = 3, Name = "王五", Email = "wangwu@example.com", Age = 35 }
        };
    }

    private void InitializeBindingSource()
    {
        // 创建和配置BindingSource
        _bindingSource = new BindingSource
        {
            DataSource = _customers
        };
    }

    private void InitializeDataGridView()
    {
        // 创建DataGridView
        _dataGridView = new DataGridView
        {
            Location = new Point(10, 10),
            Size = new Size(450, 200),
            AutoGenerateColumns = false,
            DataSource = _bindingSource
        };

        // 添加列
        _dataGridView.Columns.Add(new DataGridViewTextBoxColumn
        {
            Name = "Id",
            DataPropertyName = "Id",
            HeaderText = "ID",
            Width = 50,
            ReadOnly = true
        });

        _dataGridView.Columns.Add(new DataGridViewTextBoxColumn
        {
            Name = "Name",
            DataPropertyName = "Name",
            HeaderText = "姓名",
            Width = 100
        });

        _dataGridView.Columns.Add(new DataGridViewTextBoxColumn
        {
            Name = "Email",
            DataPropertyName = "Email",
            HeaderText = "邮箱",
            Width = 150
        });

        _dataGridView.Columns.Add(new DataGridViewTextBoxColumn
        {
            Name = "Age",
            DataPropertyName = "Age",
            HeaderText = "年龄",
            Width = 80
        });

        // 订阅DataGridView事件
        _dataGridView.CellValueChanged += DataGridView_CellValueChanged;
        _dataGridView.UserDeletingRow += DataGridView_UserDeletingRow;

        // 添加到表单
        Controls.Add(_dataGridView);
    }

    private void InitializeControls()
    {
        // 创建按钮
        _addButton = new Button
        {
            Text = "添加",
            Location = new Point(10, 220),
            Size = new Size(80, 30)
        };

        _deleteButton = new Button
        {
            Text = "删除",
            Location = new Point(100, 220),
            Size = new Size(80, 30)
        };

        _saveButton = new Button
        {
            Text = "保存",
            Location = new Point(190, 220),
            Size = new Size(80, 30)
        };

        // 添加到表单
        Controls.Add(_addButton);
        Controls.Add(_deleteButton);
        Controls.Add(_saveButton);

        // 绑定事件
        _addButton.Click += AddButton_Click;
        _deleteButton.Click += DeleteButton_Click;
        _saveButton.Click += SaveButton_Click;
    }

    private void DataGridView_CellValueChanged(object sender, DataGridViewCellEventArgs e)
    {
        // 单元格值变更时的处理
        if (e.RowIndex >= 0 && e.ColumnIndex >= 0)
        {
            var customer = (Customer)_bindingSource.List[e.RowIndex];
            Console.WriteLine($"单元格值变更: {customer.Name}");
        }
    }

    private void DataGridView_UserDeletingRow(object sender, DataGridViewRowCancelEventArgs e)
    {
        // 用户删除行时的处理
        var customer = (Customer)e.Row.DataBoundItem;
        Console.WriteLine($"删除行: {customer.Name}");
    }

    private void AddButton_Click(object sender, EventArgs e)
    {
        // 添加新记录
        var newCustomer = new Customer
        {
            Id = _customers.Max(c => c.Id) + 1,
            Name = "新客户",
            Email = "newcustomer@example.com",
            Age = 0
        };

        _customers.Add(newCustomer);
        _bindingSource.ResetBindings(false);
    }

    private void DeleteButton_Click(object sender, EventArgs e)
    {
        // 删除选中记录
        if (_dataGridView.SelectedRows.Count > 0)
        {
            var selectedRow = _dataGridView.SelectedRows[0];
            var customer = (Customer)selectedRow.DataBoundItem;
            _customers.Remove(customer);
            _bindingSource.ResetBindings(false);
        }
    }

    private void SaveButton_Click(object sender, EventArgs e)
    {
        // 保存数据
        _bindingSource.EndEdit();
        Console.WriteLine("数据已保存");
        // 这里可以添加保存到数据库的逻辑
    }
}

// 数据模型类
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    public int Age { get; set; }
}
```

**Pattern 3:** 双向绑定模式
```csharp
// 双向绑定模式
public class MainForm : Form
{
    private BindingSource _bindingSource;
    private CustomerViewModel _customerViewModel;
    private TextBox _nameTextBox;
    private TextBox _emailTextBox;
    private CheckBox _isActiveCheckBox;
    private Label _statusLabel;

    public MainForm()
    {
        InitializeComponent();
        InitializeViewModel();
        InitializeBindingSource();
        InitializeControls();
    }

    private void InitializeViewModel()
    {
        // 初始化ViewModel
        _customerViewModel = new CustomerViewModel
        {
            Name = "张三",
            Email = "zhangsan@example.com",
            IsActive = true
        };

        // 订阅ViewModel的属性变更事件
        _customerViewModel.PropertyChanged += ViewModel_PropertyChanged;
    }

    private void InitializeBindingSource()
    {
        // 创建和配置BindingSource
        _bindingSource = new BindingSource
        {
            DataSource = _customerViewModel
        };
    }

    private void InitializeControls()
    {
        // 创建控件
        _nameTextBox = new TextBox
        {
            Location = new Point(100, 20),
            Size = new Size(200, 20)
        };

        _emailTextBox = new TextBox
        {
            Location = new Point(100, 50),
            Size = new Size(200, 20)
        };

        _isActiveCheckBox = new CheckBox
        {
            Location = new Point(100, 80),
            Size = new Size(100, 20),
            Text = "激活"
        };

        _statusLabel = new Label
        {
            Location = new Point(20, 120),
            Size = new Size(300, 20),
            Text = "状态: 就绪"
        };

        var nameLabel = new Label
        {
            Text = "姓名:",
            Location = new Point(20, 20),
            Size = new Size(80, 20)
        };

        var emailLabel = new Label
        {
            Text = "邮箱:",
            Location = new Point(20, 50),
            Size = new Size(80, 20)
        };

        // 添加控件到表单
        Controls.Add(nameLabel);
        Controls.Add(_nameTextBox);
        Controls.Add(emailLabel);
        Controls.Add(_emailTextBox);
        Controls.Add(_isActiveCheckBox);
        Controls.Add(_statusLabel);

        // 双向绑定控件到BindingSource
        _nameTextBox.DataBindings.Add("Text", _bindingSource, "Name", true, DataSourceUpdateMode.OnPropertyChanged);
        _emailTextBox.DataBindings.Add("Text", _bindingSource, "Email", true, DataSourceUpdateMode.OnPropertyChanged);
        _isActiveCheckBox.DataBindings.Add("Checked", _bindingSource, "IsActive", true, DataSourceUpdateMode.OnPropertyChanged);
    }

    private void ViewModel_PropertyChanged(object sender, PropertyChangedEventArgs e)
    {
        // ViewModel属性变更时的处理
        _statusLabel.Text = $"状态: {e.PropertyName} 已变更为 {GetPropertyValue(_customerViewModel, e.PropertyName)}";
        Console.WriteLine($"{e.PropertyName} 已变更为 {GetPropertyValue(_customerViewModel, e.PropertyName)}");
    }

    private object GetPropertyValue(object obj, string propertyName)
    {
        return obj.GetType().GetProperty(propertyName)?.GetValue(obj);
    }
}

// 实现INotifyPropertyChanged的ViewModel类
public class CustomerViewModel : INotifyPropertyChanged
{
    private string _name;
    private string _email;
    private bool _isActive;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        get => _name;
        set
        {
            if (_name != value)
            {
                _name = value;
                OnPropertyChanged(nameof(Name));
            }
        }
    }

    public string Email
    {
        get => _email;
        set
        {
            if (_email != value)
            {
                _email = value;
                OnPropertyChanged(nameof(Email));
            }
        }
    }

    public bool IsActive
    {
        get => _isActive;
        set
        {
            if (_isActive != value)
            {
                _isActive = value;
                OnPropertyChanged(nameof(IsActive));
            }
        }
    }

    protected virtual void OnPropertyChanged(string propertyName)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

**Pattern 4:** 数据验证
```csharp
// 数据验证
public class MainForm : Form
{
    private BindingSource _bindingSource;
    private Customer _customer;
    private TextBox _nameTextBox;
    private TextBox _emailTextBox;
    private TextBox _ageTextBox;
    private Button _saveButton;
    private ErrorProvider _errorProvider;

    public MainForm()
    {
        InitializeComponent();
        InitializeData();
        InitializeBindingSource();
        InitializeErrorProvider();
        InitializeControls();
    }

    private void InitializeData()
    {
        // 初始化数据
        _customer = new Customer
        {
            Name = "",
            Email = "",
            Age = 0
        };
    }

    private void InitializeBindingSource()
    {
        // 创建和配置BindingSource
        _bindingSource = new BindingSource
        {
            DataSource = _customer
        };
    }

    private void InitializeErrorProvider()
    {
        // 创建ErrorProvider
        _errorProvider = new ErrorProvider
        {
            BlinkStyle = ErrorBlinkStyle.NeverBlink
        };
    }

    private void InitializeControls()
    {
        // 创建控件
        _nameTextBox = new TextBox
        {
            Location = new Point(100, 20),
            Size = new Size(200, 20)
        };

        _emailTextBox = new TextBox
        {
            Location = new Point(100, 50),
            Size = new Size(200, 20)
        };

        _ageTextBox = new TextBox
        {
            Location = new Point(100, 80),
            Size = new Size(100, 20)
        };

        _saveButton = new Button
        {
            Text = "保存",
            Location = new Point(100, 110),
            Size = new Size(80, 30)
        };

        var nameLabel = new Label
        {
            Text = "姓名:",
            Location = new Point(20, 20),
            Size = new Size(80, 20)
        };

        var emailLabel = new Label
        {
            Text = "邮箱:",
            Location = new Point(20, 50),
            Size = new Size(80, 20)
        };

        var ageLabel = new Label
        {
            Text = "年龄:",
            Location = new Point(20, 80),
            Size = new Size(80, 20)
        };

        // 添加控件到表单
        Controls.Add(nameLabel);
        Controls.Add(_nameTextBox);
        Controls.Add(emailLabel);
        Controls.Add(_emailTextBox);
        Controls.Add(ageLabel);
        Controls.Add(_ageTextBox);
        Controls.Add(_saveButton);

        // 绑定控件到BindingSource
        _nameTextBox.DataBindings.Add("Text", _bindingSource, "Name", true, DataSourceUpdateMode.OnPropertyChanged);
        _emailTextBox.DataBindings.Add("Text", _bindingSource, "Email", true, DataSourceUpdateMode.OnPropertyChanged);
        _ageTextBox.DataBindings.Add("Text", _bindingSource, "Age", true, DataSourceUpdateMode.OnPropertyChanged, 0);

        // 订阅验证事件
        _nameTextBox.Validating += NameTextBox_Validating;
        _emailTextBox.Validating += EmailTextBox_Validating;
        _ageTextBox.Validating += AgeTextBox_Validating;

        // 绑定保存按钮事件
        _saveButton.Click += SaveButton_Click;
    }

    private void NameTextBox_Validating(object sender, CancelEventArgs e)
    {
        if (string.IsNullOrWhiteSpace(_nameTextBox.Text))
        {
            _errorProvider.SetError(_nameTextBox, "姓名不能为空");
            e.Cancel = true;
        }
        else
        {
            _errorProvider.SetError(_nameTextBox, "");
        }
    }

    private void EmailTextBox_Validating(object sender, CancelEventArgs e)
    {
        if (string.IsNullOrWhiteSpace(_emailTextBox.Text))
        {
            _errorProvider.SetError(_emailTextBox, "邮箱不能为空");
            e.Cancel = true;
        }
        else if (!IsValidEmail(_emailTextBox.Text))
        {
            _errorProvider.SetError(_emailTextBox, "邮箱格式不正确");
            e.Cancel = true;
        }
        else
        {
            _errorProvider.SetError(_emailTextBox, "");
        }
    }

    private void AgeTextBox_Validating(object sender, CancelEventArgs e)
    {
        if (!int.TryParse(_ageTextBox.Text, out int age) || age < 0 || age > 150)
        {
            _errorProvider.SetError(_ageTextBox, "年龄必须是0-150之间的整数");
            e.Cancel = true;
        }
        else
        {
            _errorProvider.SetError(_ageTextBox, "");
        }
    }

    private bool IsValidEmail(string email)
    {
        try
        {
            var addr = new System.Net.Mail.MailAddress(email);
            return addr.Address == email;
        }
        catch
        {
            return false;
        }
    }

    private void SaveButton_Click(object sender, EventArgs e)
    {
        // 验证所有控件
        if (ValidateChildren())
        {
            _bindingSource.EndEdit();
            Console.WriteLine("数据验证通过，保存成功");
            // 这里可以添加保存到数据库的逻辑
        }
        else
        {
            Console.WriteLine("数据验证失败");
        }
    }
}

// 数据模型类
public class Customer
{
    public string Name { get; set; }
    public string Email { get; set; }
    public int Age { get; set; }
}
```

**Pattern 5:** 绑定异常处理
```csharp
// 绑定异常处理
public class MainForm : Form
{
    private BindingSource _bindingSource;
    private Customer _customer;
    private TextBox _nameTextBox;
    private TextBox _ageTextBox;
    private Button _loadButton;
    private Button _saveButton;

    public MainForm()
    {
        InitializeComponent();
        InitializeData();
        InitializeBindingSource();
        InitializeControls();
    }

    private void InitializeData()
    {
        // 初始化数据
        _customer = new Customer
        {
            Name = "张三",
            Age = 25
        };
    }

    private void InitializeBindingSource()
    {
        // 创建和配置BindingSource
        _bindingSource = new BindingSource
        {
            DataSource = _customer
        };

        // 订阅BindingComplete事件处理异常
        _bindingSource.BindingComplete += BindingSource_BindingComplete;
    }

    private void InitializeControls()
    {
        // 创建控件
        _nameTextBox = new TextBox
        {
            Location = new Point(100, 20),
            Size = new Size(200, 20)
        };

        _ageTextBox = new TextBox
        {
            Location = new Point(100, 50),
            Size = new Size(100, 20)
        };

        _loadButton = new Button
        {
            Text = "加载",
            Location = new Point(100, 80),
            Size = new Size(80, 30)
        };

        _saveButton = new Button
        {
            Text = "保存",
            Location = new Point(190, 80),
            Size = new Size(80, 30)
        };

        var nameLabel = new Label
        {
            Text = "姓名:",
            Location = new Point(20, 20),
            Size = new Size(80, 20)
        };

        var ageLabel = new Label
        {
            Text = "年龄:",
            Location = new Point(20, 50),
            Size = new Size(80, 20)
        };

        // 添加控件到表单
        Controls.Add(nameLabel);
        Controls.Add(_nameTextBox);
        Controls.Add(ageLabel);
        Controls.Add(_ageTextBox);
        Controls.Add(_loadButton);
        Controls.Add(_saveButton);

        // 绑定控件到BindingSource
        _nameTextBox.DataBindings.Add("Text", _bindingSource, "Name", true, DataSourceUpdateMode.OnPropertyChanged);
        _ageTextBox.DataBindings.Add("Text", _bindingSource, "Age", true, DataSourceUpdateMode.OnPropertyChanged, 0);

        // 绑定按钮事件
        _loadButton.Click += LoadButton_Click;
        _saveButton.Click += SaveButton_Click;
    }

    private void BindingSource_BindingComplete(object sender, BindingCompleteEventArgs e)
    {
        // 处理绑定异常
        if (e.Exception != null)
        {
            Console.WriteLine($"绑定异常: {e.Exception.Message}");
            MessageBox.Show($"绑定错误: {e.Exception.Message}", "错误", MessageBoxButtons.OK, MessageBoxIcon.Error);
            e.Handled = true;
        }
    }

    private void LoadButton_Click(object sender, EventArgs e)
    {
        try
        {
            // 模拟从数据库加载数据时可能发生的异常
            throw new Exception("数据库连接失败");
            
            // 正常加载逻辑
            // _customer = Database.LoadCustomer();
            // _bindingSource.ResetBindings(false);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"加载异常: {ex.Message}");
            MessageBox.Show($"加载错误: {ex.Message}", "错误", MessageBoxButtons.OK, MessageBoxIcon.Error);
        }
    }

    private void SaveButton_Click(object sender, EventArgs e)
    {
        try
        {
            // 结束编辑
            _bindingSource.EndEdit();
            
            // 模拟保存数据时可能发生的异常
            // throw new Exception("保存失败");
            
            // 正常保存逻辑
            Console.WriteLine("数据保存成功");
            MessageBox.Show("保存成功", "信息", MessageBoxButtons.OK, MessageBoxIcon.Information);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"保存异常: {ex.Message}");
            MessageBox.Show($"保存错误: {ex.Message}", "错误", MessageBoxButtons.OK, MessageBoxIcon.Error);
        }
    }
}

// 数据模型类
public class Customer
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

## Examples

### Example 1: 配置BindingSource
- Input: 需要创建一个显示客户数据的表单
- Steps:
  1. 创建数据模型类
  2. 初始化数据
  3. 创建和配置BindingSource
  4. 绑定控件到BindingSource
  5. 测试数据绑定
- Expected output / acceptance: 控件正确显示和更新数据

### Example 2: 实现DataGridView数据绑定
- Input: 需要创建一个显示客户列表的DataGridView
- Steps:
  1. 创建数据模型类
  2. 初始化数据列表
  3. 创建和配置BindingSource
  4. 创建和配置DataGridView
  5. 绑定DataGridView到BindingSource
  6. 测试数据显示和编辑
- Expected output / acceptance: DataGridView正确显示和编辑数据

### Example 3: 使用双向绑定模式
- Input: 需要创建一个支持双向数据绑定的表单
- Steps:
  1. 创建实现INotifyPropertyChanged的ViewModel类
  2. 初始化ViewModel
  3. 创建和配置BindingSource
  4. 绑定控件到BindingSource
  5. 测试双向数据绑定
- Expected output / acceptance: 控件和数据源之间实现双向数据同步

### Example 4: 进行数据验证
- Input: 需要创建一个带数据验证的表单
- Steps:
  1. 创建数据模型类
  2. 初始化数据
  3. 创建和配置BindingSource
  4. 创建ErrorProvider
  5. 绑定控件到BindingSource
  6. 实现验证逻辑
  7. 测试数据验证
- Expected output / acceptance: 表单能够正确验证输入数据

### Example 5: 处理绑定异常
- Input: 需要创建一个能够处理绑定异常的表单
- Steps:
  1. 创建数据模型类
  2. 初始化数据
  3. 创建和配置BindingSource
  4. 订阅BindingComplete事件
  5. 绑定控件到BindingSource
  6. 实现异常处理逻辑
  7. 测试异常处理
- Expected output / acceptance: 表单能够正确处理绑定过程中的异常

## References

- `references/index.md`: 数据绑定最佳实践导航
- `references/bindingsource-configuration.md`: BindingSource配置指南
- `references/datagridview-binding.md`: DataGridView数据绑定指南
- `references/two-way-binding.md`: 双向绑定模式指南
- `references/data-validation.md`: 数据验证指南
- `references/binding-exception-handling.md`: 绑定异常处理指南

## Maintenance

- Sources: WinForms官方文档和数据绑定最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现