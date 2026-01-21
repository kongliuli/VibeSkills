# Blazor路由配置师 (blazor-router-configurator)

为C# Blazor项目提供专业的路由配置和管理能力，确保路由系统具有规范、一致、高效的实现。

## 主要功能

- **@page指令路由**：配置和管理@page指令路由
- **路由参数提取**：提取和使用路由参数
- **导航管理**：使用NavigationManager进行导航管理
- **查询字符串处理**：处理和管理查询字符串
- **路由守卫**：实现和配置路由守卫

## 使用场景

- 需要配置@page指令路由
- 需要提取路由参数
- 需要使用NavigationManager进行导航管理
- 需要处理查询字符串
- 需要实现路由守卫
- 需要优化现有路由实现

## 快速开始

### @page指令路由
```razor
<!-- 基本路由配置 -->
@page "/"

<h1>Home Page</h1>
<p>Welcome to the home page!</p>

<!-- 带参数的路由 -->
@page "/user/{id}"

<h1>User Profile</h1>
<p>User ID: @Id</p>

@code {
    [Parameter]
    public string Id { get; set; }
}

<!-- 多个路由 -->
@page "/products"
@page "/items"

<h1>Products</h1>
```

### 路由参数提取
```razor
<!-- 路由参数提取 -->
@page "/product/{id:int}"
@page "/product/{id:int}/{slug?}"

<h1>Product Details</h1>
<p>Product ID: @Id</p>
@if (!string.IsNullOrEmpty(Slug))
{
    <p>Slug: @Slug</p>
}

@code {
    [Parameter]
    public int Id { get; set; }

    [Parameter]
    public string Slug { get; set; }
}
```

### 导航管理(NavigationManager)
```razor
<!-- NavigationManager使用 -->
@inject NavigationManager NavigationManager

<h1>Navigation Example</h1>

<button @onclick="NavigateToHome">Go Home</button>
<button @onclick="NavigateToProduct">Go to Product 1</button>
<button @onclick="GoBack">Go Back</button>

@code {
    private void NavigateToHome()
    {
        NavigationManager.NavigateTo("/");
    }

    private void NavigateToProduct()
    {
        NavigationManager.NavigateTo("/product/1");
    }

    private void GoBack()
    {
        NavigationManager.NavigateTo("/", forceLoad: true);
    }
}
```

### 查询字符串处理
```razor
<!-- 查询字符串处理 -->
@inject NavigationManager NavigationManager

<h1>Search Page</h1>

<form @onsubmit="HandleSearch">
    <input type="text" @bind="searchQuery" placeholder="Search..." />
    <button type="submit">Search</button>
</form>

@code {
    private string searchQuery;

    protected override void OnInitialized()
    {
        // 从查询字符串加载参数
        LoadFromQueryString();
    }

    private void LoadFromQueryString()
    {
        var uri = new Uri(NavigationManager.Uri);
        var queryParams = System.Web.HttpUtility.ParseQueryString(uri.Query);
        searchQuery = queryParams["q"] ?? string.Empty;
    }

    private void HandleSearch(EventArgs e)
    {
        var url = $"/search?q={Uri.EscapeDataString(searchQuery)}";
        NavigationManager.NavigateTo(url);
    }
}
```

### 路由守卫
```razor
<!-- 路由守卫实现 -->
@inject NavigationManager NavigationManager
@inject AuthenticationStateProvider AuthenticationStateProvider

<h1>Secure Page</h1>

@if (isAuthenticated)
{
    <p>Welcome to the secure page!</p>
}
else
{
    <p>You must be logged in to access this page.</p>
    <button @onclick="NavigateToLogin">Go to Login</button>
}

@code {
    private bool isAuthenticated;

    protected override async Task OnInitializedAsync()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        isAuthenticated = authState.User.Identity.IsAuthenticated;

        if (!isAuthenticated)
        {
            NavigationManager.NavigateTo($"/login?returnUrl={Uri.EscapeDataString(NavigationManager.Uri)}");
        }
    }

    private void NavigateToLogin()
    {
        NavigationManager.NavigateTo($"/login?returnUrl={Uri.EscapeDataString(NavigationManager.Uri)}");
    }
}
```

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件