---
name: winforms-control-layout
description: "C# WinForms控件布局师技能：负责TableLayoutPanel配置、UserControl设计、自定义控件绘制、DPI适配、锚定和停靠策略。使用时需要配置TableLayoutPanel、设计UserControl、实现自定义控件绘制、适配DPI、配置锚定和停靠策略。"
---

# winforms-control-layout Skill

为C# WinForms项目提供专业的控件布局和设计能力，确保应用具有美观、一致、响应式的用户界面布局。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置TableLayoutPanel
- 需要设计UserControl
- 需要实现自定义控件绘制
- 需要适配DPI
- 需要配置锚定和停靠策略
- 需要优化现有控件布局实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行布局设计决策
- 不处理布局的深度性能优化（仅设计层面）
- 不负责布局的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** TableLayoutPanel配置
```csharp
// TableLayoutPanel配置
public class MainForm : Form
{
    private TableLayoutPanel _tableLayoutPanel;
    private Button _button1;
    private Button _button2;
    private TextBox _textBox;
    private Label _label;

    public MainForm()
    {
        InitializeComponent();
        InitializeTableLayoutPanel();
    }

    private void InitializeTableLayoutPanel()
    {
        // 创建TableLayoutPanel
        _tableLayoutPanel = new TableLayoutPanel
        {
            Dock = DockStyle.Fill,
            ColumnCount = 2,
            RowCount = 2,
            Padding = new Padding(10),
            CellBorderStyle = TableLayoutPanelCellBorderStyle.Single
        };

        // 配置列样式
        _tableLayoutPanel.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 30F));
        _tableLayoutPanel.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 70F));

        // 配置行样式
        _tableLayoutPanel.RowStyles.Add(new RowStyle(SizeType.AutoSize));
        _tableLayoutPanel.RowStyles.Add(new RowStyle(SizeType.AutoSize));

        // 创建控件
        _label = new Label
        {
            Text = "输入文本:",
            AutoSize = true,
            Margin = new Padding(5)
        };

        _textBox = new TextBox
        {
            Dock = DockStyle.Fill,
            Margin = new Padding(5)
        };

        _button1 = new Button
        {
            Text = "按钮1",
            Dock = DockStyle.Fill,
            Margin = new Padding(5)
        };

        _button2 = new Button
        {
            Text = "按钮2",
            Dock = DockStyle.Fill,
            Margin = new Padding(5)
        };

        // 添加控件到TableLayoutPanel
        _tableLayoutPanel.Controls.Add(_label, 0, 0);
        _tableLayoutPanel.Controls.Add(_textBox, 1, 0);
        _tableLayoutPanel.Controls.Add(_button1, 0, 1);
        _tableLayoutPanel.Controls.Add(_button2, 1, 1);

        // 添加TableLayoutPanel到表单
        Controls.Add(_tableLayoutPanel);
    }
}
```

**Pattern 2:** UserControl设计
```csharp
// UserControl设计
public partial class CustomerInfoControl : UserControl
{
    // 公共属性
    public string CustomerName
    {
        get => txtName.Text;
        set => txtName.Text = value;
    }

    public string CustomerEmail
    {
        get => txtEmail.Text;
        set => txtEmail.Text = value;
    }

    public string CustomerPhone
    {
        get => txtPhone.Text;
        set => txtPhone.Text = value;
    }

    // 事件
    public event EventHandler SaveClicked;
    public event EventHandler CancelClicked;

    public CustomerInfoControl()
    {
        InitializeComponent();
        InitializeLayout();
    }

    private void InitializeLayout()
    {
        // 创建TableLayoutPanel用于布局
        var tableLayoutPanel = new TableLayoutPanel
        {
            Dock = DockStyle.Fill,
            ColumnCount = 2,
            RowCount = 4,
            Padding = new Padding(10),
            CellBorderStyle = TableLayoutPanelCellBorderStyle.None
        };

        // 配置列和行
        tableLayoutPanel.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 30F));
        tableLayoutPanel.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 70F));

        for (int i = 0; i < tableLayoutPanel.RowCount; i++)
        {
            tableLayoutPanel.RowStyles.Add(new RowStyle(SizeType.AutoSize));
        }

        // 创建控件
        var lblName = new Label { Text = "姓名:", AutoSize = true, Margin = new Padding(5) };
        var lblEmail = new Label { Text = "邮箱:", AutoSize = true, Margin = new Padding(5) };
        var lblPhone = new Label { Text = "电话:", AutoSize = true, Margin = new Padding(5) };

        txtName = new TextBox { Dock = DockStyle.Fill, Margin = new Padding(5) };
        txtEmail = new TextBox { Dock = DockStyle.Fill, Margin = new Padding(5) };
        txtPhone = new TextBox { Dock = DockStyle.Fill, Margin = new Padding(5) };

        var buttonPanel = new Panel { Dock = DockStyle.Fill, Margin = new Padding(5) };
        var btnSave = new Button { Text = "保存", Location = new Point(0, 0), Size = new Size(80, 30) };
        var btnCancel = new Button { Text = "取消", Location = new Point(90, 0), Size = new Size(80, 30) };

        buttonPanel.Controls.Add(btnSave);
        buttonPanel.Controls.Add(btnCancel);

        // 添加控件到TableLayoutPanel
        tableLayoutPanel.Controls.Add(lblName, 0, 0);
        tableLayoutPanel.Controls.Add(txtName, 1, 0);
        tableLayoutPanel.Controls.Add(lblEmail, 0, 1);
        tableLayoutPanel.Controls.Add(txtEmail, 1, 1);
        tableLayoutPanel.Controls.Add(lblPhone, 0, 2);
        tableLayoutPanel.Controls.Add(txtPhone, 1, 2);
        tableLayoutPanel.SetColumnSpan(buttonPanel, 2);
        tableLayoutPanel.Controls.Add(buttonPanel, 0, 3);

        // 添加TableLayoutPanel到UserControl
        Controls.Add(tableLayoutPanel);

        // 绑定事件
        btnSave.Click += (sender, e) => SaveClicked?.Invoke(this, e);
        btnCancel.Click += (sender, e) => CancelClicked?.Invoke(this, e);
    }

    // 重置表单
    public void Reset()
    {
        txtName.Text = string.Empty;
        txtEmail.Text = string.Empty;
        txtPhone.Text = string.Empty;
    }

    // 验证输入
    public bool ValidateInput()
    {
        if (string.IsNullOrWhiteSpace(txtName.Text))
        {
            MessageBox.Show("请输入姓名", "错误", MessageBoxButtons.OK, MessageBoxIcon.Error);
            txtName.Focus();
            return false;
        }

        if (string.IsNullOrWhiteSpace(txtEmail.Text))
        {
            MessageBox.Show("请输入邮箱", "错误", MessageBoxButtons.OK, MessageBoxIcon.Error);
            txtEmail.Focus();
            return false;
        }

        return true;
    }
}

// 使用UserControl
public class MainForm : Form
{
    private CustomerInfoControl _customerInfoControl;

    public MainForm()
    {
        InitializeComponent();
        InitializeCustomerInfoControl();
    }

    private void InitializeCustomerInfoControl()
    {
        _customerInfoControl = new CustomerInfoControl
        {
            Dock = DockStyle.Fill,
            Margin = new Padding(10)
        };

        // 绑定事件
        _customerInfoControl.SaveClicked += CustomerInfoControl_SaveClicked;
        _customerInfoControl.CancelClicked += CustomerInfoControl_CancelClicked;

        Controls.Add(_customerInfoControl);
    }

    private void CustomerInfoControl_SaveClicked(object sender, EventArgs e)
    {
        if (_customerInfoControl.ValidateInput())
        {
            // 保存客户信息
            MessageBox.Show("保存成功", "信息", MessageBoxButtons.OK, MessageBoxIcon.Information);
        }
    }

    private void CustomerInfoControl_CancelClicked(object sender, EventArgs e)
    {
        _customerInfoControl.Reset();
    }
}
```

**Pattern 3:** 自定义控件绘制
```csharp
// 自定义控件绘制
public class CustomProgressBar : Control
{
    // 属性
    private int _value = 0;
    public int Value
    {
        get => _value;
        set
        {
            if (value < 0) value = 0;
            if (value > 100) value = 100;
            _value = value;
            Invalidate(); // 触发重绘
        }
    }

    public Color ProgressColor { get; set; } = Color.Blue;
    public Color BackColor { get; set; } = Color.LightGray;
    public int BorderWidth { get; set; } = 1;
    public Color BorderColor { get; set; } = Color.Gray;

    public CustomProgressBar()
    {
        DoubleBuffered = true; // 减少闪烁
        Size = new Size(200, 20);
    }

    // 重写OnPaint方法进行绘制
    protected override void OnPaint(PaintEventArgs e)
    {
        base.OnPaint(e);
        Graphics g = e.Graphics;
        g.SmoothingMode = System.Drawing.Drawing2D.SmoothingMode.AntiAlias;

        // 计算尺寸
        int width = ClientSize.Width - (BorderWidth * 2);
        int height = ClientSize.Height - (BorderWidth * 2);
        int progressWidth = (int)((width * Value) / 100.0);

        // 绘制背景
        using (SolidBrush backBrush = new SolidBrush(BackColor))
        {
            g.FillRectangle(backBrush, BorderWidth, BorderWidth, width, height);
        }

        // 绘制进度
        using (SolidBrush progressBrush = new SolidBrush(ProgressColor))
        {
            g.FillRectangle(progressBrush, BorderWidth, BorderWidth, progressWidth, height);
        }

        // 绘制边框
        using (Pen borderPen = new Pen(BorderColor, BorderWidth))
        {
            g.DrawRectangle(borderPen, 0, 0, ClientSize.Width - 1, ClientSize.Height - 1);
        }

        // 绘制文本
        string text = $"{Value}%";
        using (Font font = new Font(FontFamily.GenericSansSerif, 10, FontStyle.Bold))
        using (SolidBrush textBrush = new SolidBrush(ForeColor))
        {
            SizeF textSize = g.MeasureString(text, font);
            float textX = (ClientSize.Width - textSize.Width) / 2;
            float textY = (ClientSize.Height - textSize.Height) / 2;
            g.DrawString(text, font, textBrush, textX, textY);
        }
    }

    // 重写OnResize方法
    protected override void OnResize(EventArgs e)
    {
        base.OnResize(e);
        Invalidate(); // 触发重绘
    }
}

// 使用自定义控件
public class MainForm : Form
{
    private CustomProgressBar _customProgressBar;
    private Button _startButton;

    public MainForm()
    {
        InitializeComponent();
        InitializeCustomProgressBar();
    }

    private void InitializeCustomProgressBar()
    {
        _customProgressBar = new CustomProgressBar
        {
            Location = new Point(10, 10),
            Size = new Size(300, 30),
            Value = 0,
            ProgressColor = Color.Green,
            BackColor = Color.LightGray,
            BorderColor = Color.DarkGray,
            ForeColor = Color.White
        };

        _startButton = new Button
        {
            Text = "开始",
            Location = new Point(10, 50),
            Size = new Size(100, 30)
        };

        Controls.Add(_customProgressBar);
        Controls.Add(_startButton);

        _startButton.Click += StartButton_Click;
    }

    private async void StartButton_Click(object sender, EventArgs e)
    {
        _startButton.Enabled = false;
        _customProgressBar.Value = 0;

        for (int i = 0; i <= 100; i++)
        {
            _customProgressBar.Value = i;
            await Task.Delay(50);
        }

        _startButton.Enabled = true;
    }
}
```

**Pattern 4:** DPI适配
```csharp
// DPI适配
public class DpiAwareForm : Form
{
    // 原始设计DPI
    private const int DesignDpi = 96;
    // 当前DPI
    private int _currentDpi;
    // DPI缩放比例
    private float _dpiScale;

    public DpiAwareForm()
    {
        // 启用DPI感知
        EnableDpiAwareness();
        
        // 初始化DPI相关值
        _currentDpi = GetCurrentDpi();
        _dpiScale = (float)_currentDpi / DesignDpi;

        // 处理DPI变更事件
        if (SystemInformation.UserInteractive)
        {
            // 在Windows 10 1607及以上版本，可以订阅DPI变更事件
            this.DpiChanged += DpiAwareForm_DpiChanged;
        }

        InitializeComponent();
        InitializeControls();
    }

    // 启用DPI感知
    private void EnableDpiAwareness()
    {
        // 在应用程序清单中设置DPI感知
        // 或者使用代码启用
        try
        {
            // 对于.NET Framework 4.7及以上，可以使用以下API
            if (Environment.OSVersion.Version >= new Version(6, 1)) // Windows 7+
            {
                // 这里可以添加DPI感知相关代码
            }
        }
        catch { }
    }

    // 获取当前DPI
    private int GetCurrentDpi()
    {
        using (Graphics g = CreateGraphics())
        {
            return (int)g.DpiX;
        }
    }

    // DPI变更事件处理
    private void DpiAwareForm_DpiChanged(object sender, DpiChangedEventArgs e)
    {
        _currentDpi = e.NewDpi.X;
        _dpiScale = (float)_currentDpi / DesignDpi;
        
        // 重新布局控件
        ScaleControls();
    }

    // 缩放控件
    private void ScaleControls()
    {
        foreach (Control control in Controls)
        {
            ScaleControl(control);
        }
    }

    // 缩放单个控件
    private void ScaleControl(Control control)
    {
        // 缩放位置
        control.Location = new Point(
            (int)(control.Location.X * _dpiScale),
            (int)(control.Location.Y * _dpiScale)
        );

        // 缩放大小
        control.Size = new Size(
            (int)(control.Size.Width * _dpiScale),
            (int)(control.Size.Height * _dpiScale)
        );

        // 缩放字体
        control.Font = new Font(
            control.Font.FontFamily,
            control.Font.Size * _dpiScale,
            control.Font.Style
        );

        // 递归缩放子控件
        foreach (Control childControl in control.Controls)
        {
            ScaleControl(childControl);
        }
    }

    private void InitializeControls()
    {
        // 创建控件时考虑DPI缩放
        var button = new Button
        {
            Text = "按钮",
            Location = ScalePoint(new Point(10, 10)),
            Size = ScaleSize(new Size(100, 30)),
            Font = ScaleFont(new Font("Microsoft Sans Serif", 9))
        };

        var textBox = new TextBox
        {
            Text = "文本框",
            Location = ScalePoint(new Point(10, 50)),
            Size = ScaleSize(new Size(200, 30)),
            Font = ScaleFont(new Font("Microsoft Sans Serif", 9))
        };

        Controls.Add(button);
        Controls.Add(textBox);
    }

    // 辅助方法：缩放点
    private Point ScalePoint(Point point)
    {
        return new Point(
            (int)(point.X * _dpiScale),
            (int)(point.Y * _dpiScale)
        );
    }

    // 辅助方法：缩放大小
    private Size ScaleSize(Size size)
    {
        return new Size(
            (int)(size.Width * _dpiScale),
            (int)(size.Height * _dpiScale)
        );
    }

    // 辅助方法：缩放字体
    private Font ScaleFont(Font font)
    {
        return new Font(
            font.FontFamily,
            font.Size * _dpiScale,
            font.Style
        );
    }
}
```

**Pattern 5:** 锚定和停靠策略
```csharp
// 锚定和停靠策略
public class MainForm : Form
{
    private Button _dockTopButton;
    private Button _dockBottomButton;
    private Button _dockLeftButton;
    private Button _dockRightButton;
    private Button _anchorButton;
    private TextBox _anchorTextBox;
    private Panel _centerPanel;

    public MainForm()
    {
        InitializeComponent();
        InitializeControls();
    }

    private void InitializeControls()
    {
        // 设置表单属性
        this.Text = "锚定和停靠示例";
        this.Size = new Size(800, 600);

        // 创建顶部停靠按钮
        _dockTopButton = new Button
        {
            Text = "顶部停靠",
            Dock = DockStyle.Top,
            Height = 50
        };

        // 创建底部停靠按钮
        _dockBottomButton = new Button
        {
            Text = "底部停靠",
            Dock = DockStyle.Bottom,
            Height = 50
        };

        // 创建左侧停靠按钮
        _dockLeftButton = new Button
        {
            Text = "左侧停靠",
            Dock = DockStyle.Left,
            Width = 100
        };

        // 创建右侧停靠按钮
        _dockRightButton = new Button
        {
            Text = "右侧停靠",
            Dock = DockStyle.Right,
            Width = 100
        };

        // 创建中心面板
        _centerPanel = new Panel
        {
            Dock = DockStyle.Fill,
            BackColor = Color.LightGray
        };

        // 创建锚定按钮
        _anchorButton = new Button
        {
            Text = "锚定按钮",
            Location = new Point(10, 10),
            Size = new Size(120, 40),
            Anchor = AnchorStyles.Top | AnchorStyles.Left | AnchorStyles.Right
        };

        // 创建锚定文本框
        _anchorTextBox = new TextBox
        {
            Text = "锚定文本框",
            Location = new Point(10, 60),
            Size = new Size(200, 30),
            Anchor = AnchorStyles.Top | AnchorStyles.Left | AnchorStyles.Right | AnchorStyles.Bottom
        };

        // 添加控件到中心面板
        _centerPanel.Controls.Add(_anchorButton);
        _centerPanel.Controls.Add(_anchorTextBox);

        // 添加控件到表单
        Controls.Add(_dockTopButton);
        Controls.Add(_dockBottomButton);
        Controls.Add(_dockLeftButton);
        Controls.Add(_dockRightButton);
        Controls.Add(_centerPanel);
    }
}

// 复杂布局示例
public class ComplexLayoutForm : Form
{
    private SplitContainer _splitContainer;
    private ListBox _listBox;
    private Panel _detailPanel;
    private Button _actionButton;
    private TextBox _detailTextBox;

    public ComplexLayoutForm()
    {
        InitializeComponent();
        InitializeComplexLayout();
    }

    private void InitializeComplexLayout()
    {
        // 设置表单属性
        this.Text = "复杂布局示例";
        this.Size = new Size(800, 600);

        // 创建分割容器
        _splitContainer = new SplitContainer
        {
            Dock = DockStyle.Fill,
            Orientation = Orientation.Horizontal,
            SplitterDistance = 200
        };

        // 创建列表框
        _listBox = new ListBox
        {
            Dock = DockStyle.Fill
        };

        // 添加示例项
        for (int i = 1; i <= 10; i++)
        {
            _listBox.Items.Add($"项目 {i}");
        }

        // 创建详细信息面板
        _detailPanel = new Panel
        {
            Dock = DockStyle.Fill
        };

        // 创建详细信息文本框
        _detailTextBox = new TextBox
        {
            Multiline = true,
            Dock = DockStyle.Fill,
            Margin = new Padding(10),
            Text = "详细信息将显示在这里"
        };

        // 创建操作按钮
        _actionButton = new Button
        {
            Text = "执行操作",
            Dock = DockStyle.Bottom,
            Height = 40,
            Margin = new Padding(10)
        };

        // 添加控件到详细信息面板
        _detailPanel.Controls.Add(_detailTextBox);
        _detailPanel.Controls.Add(_actionButton);

        // 添加控件到分割容器
        _splitContainer.Panel1.Controls.Add(_listBox);
        _splitContainer.Panel2.Controls.Add(_detailPanel);

        // 添加分割容器到表单
        Controls.Add(_splitContainer);

        // 绑定事件
        _listBox.SelectedIndexChanged += ListBox_SelectedIndexChanged;
        _actionButton.Click += ActionButton_Click;
    }

    private void ListBox_SelectedIndexChanged(object sender, EventArgs e)
    {
        if (_listBox.SelectedItem != null)
        {
            _detailTextBox.Text = $"您选择了: {_listBox.SelectedItem.ToString()}\n\n详细信息...