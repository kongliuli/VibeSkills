# WinForms控件布局师 (control-layout)

为C# WinForms项目提供专业的控件布局和设计能力，确保应用具有美观、一致、响应式的用户界面布局。

## 主要功能

- **TableLayoutPanel配置**：使用表格布局面板创建灵活的网格布局
- **UserControl设计**：设计和开发可重用的用户控件
- **自定义控件绘制**：实现自定义控件的绘制和渲染
- **DPI适配**：确保应用在不同DPI设置下正确显示
- **锚定和停靠策略**：使用锚定和停靠属性创建响应式布局

## 使用场景

- 需要配置TableLayoutPanel
- 需要设计UserControl
- 需要实现自定义控件绘制
- 需要适配DPI
- 需要配置锚定和停靠策略
- 需要优化现有控件布局实现

## 快速开始

### TableLayoutPanel配置
```csharp
public class MainForm : Form
{
    private TableLayoutPanel _tableLayoutPanel;

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
        var label = new Label { Text = "输入文本:", AutoSize = true, Margin = new Padding(5) };
        var textBox = new TextBox { Dock = DockStyle.Fill, Margin = new Padding(5) };
        var button1 = new Button { Text = "按钮1", Dock = DockStyle.Fill, Margin = new Padding(5) };
        var button2 = new Button { Text = "按钮2", Dock = DockStyle.Fill, Margin = new Padding(5) };

        // 添加控件到TableLayoutPanel
        _tableLayoutPanel.Controls.Add(label, 0, 0);
        _tableLayoutPanel.Controls.Add(textBox, 1, 0);
        _tableLayoutPanel.Controls.Add(button1, 0, 1);
        _tableLayoutPanel.Controls.Add(button2, 1, 1);

        // 添加TableLayoutPanel到表单
        Controls.Add(_tableLayoutPanel);
    }
}
```

### UserControl设计
```csharp
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
            RowCount = 3,
            Padding = new Padding(10)
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
        txtName = new TextBox { Dock = DockStyle.Fill, Margin = new Padding(5) };
        txtEmail = new TextBox { Dock = DockStyle.Fill, Margin = new Padding(5) };

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
        tableLayoutPanel.SetColumnSpan(buttonPanel, 2);
        tableLayoutPanel.Controls.Add(buttonPanel, 0, 2);

        // 添加TableLayoutPanel到UserControl
        Controls.Add(tableLayoutPanel);

        // 绑定事件
        btnSave.Click += (sender, e) => SaveClicked?.Invoke(this, e);
        btnCancel.Click += (sender, e) => CancelClicked?.Invoke(this, e);
    }
}
```

### 自定义控件绘制
```csharp
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

        // 绘制背景
        using (SolidBrush backBrush = new SolidBrush(BackColor))
        {
            g.FillRectangle(backBrush, 0, 0, ClientSize.Width, ClientSize.Height);
        }

        // 绘制进度
        int progressWidth = (int)((ClientSize.Width * Value) / 100.0);
        using (SolidBrush progressBrush = new SolidBrush(ProgressColor))
        {
            g.FillRectangle(progressBrush, 0, 0, progressWidth, ClientSize.Height);
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
}
```

### DPI适配
```csharp
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
        // 初始化DPI相关值
        _currentDpi = GetCurrentDpi();
        _dpiScale = (float)_currentDpi / DesignDpi;

        // 处理DPI变更事件
        this.DpiChanged += DpiAwareForm_DpiChanged;

        InitializeComponent();
        InitializeControls();
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
        // 缩放位置和大小
        control.Location = new Point(
            (int)(control.Location.X * _dpiScale),
            (int)(control.Location.Y * _dpiScale)
        );

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
}
```

### 锚定和停靠策略
```csharp
public class MainForm : Form
{
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
        var dockTopButton = new Button
        {
            Text = "顶部停靠",
            Dock = DockStyle.Top,
            Height = 50
        };

        // 创建底部停靠按钮
        var dockBottomButton = new Button
        {
            Text = "底部停靠",
            Dock = DockStyle.Bottom,
            Height = 50
        };

        // 创建左侧停靠按钮
        var dockLeftButton = new Button
        {
            Text = "左侧停靠",
            Dock = DockStyle.Left,
            Width = 100
        };

        // 创建右侧停靠按钮
        var dockRightButton = new Button
        {
            Text = "右侧停靠",
            Dock = DockStyle.Right,
            Width = 100
        };

        // 创建中心面板
        var centerPanel = new Panel
        {
            Dock = DockStyle.Fill,
            BackColor = Color.LightGray
        };

        // 创建锚定按钮
        var anchorButton = new Button
        {
            Text = "锚定按钮",
            Location = new Point(10, 10),
            Size = new Size(120, 40),
            Anchor = AnchorStyles.Top | AnchorStyles.Left | AnchorStyles.Right
        };

        // 添加控件到中心面板
        centerPanel.Controls.Add(anchorButton);

        // 添加控件到表单
        Controls.Add(dockTopButton);
        Controls.Add(dockBottomButton);
        Controls.Add(dockLeftButton);
        Controls.Add(dockRightButton);
        Controls.Add(centerPanel);
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件