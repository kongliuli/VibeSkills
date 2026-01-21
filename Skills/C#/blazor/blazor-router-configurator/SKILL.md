---
name: blazor-router-configurator
description: "C# Blazor路由配置师技能：负责@page指令路由、路由参数提取、导航管理(NavigationManager)、查询字符串处理、路由守卫。使用时需要配置@page指令路由、提取路由参数、使用NavigationManager进行导航管理、处理查询字符串、实现路由守卫。"
---

# blazor-router-configurator Skill

为C# Blazor项目提供专业的路由配置和管理能力，确保路由系统具有规范、一致、高效的实现。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置@page指令路由
- 需要提取路由参数
- 需要使用NavigationManager进行导航管理
- 需要处理查询字符串
- 需要实现路由守卫
- 需要优化现有路由实现

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行路由设计决策
- 不处理路由的深度性能优化（仅设计层面）
- 不负责路由的测试和调试（仅实现）

## Quick Reference

### Common Patterns

**Pattern 1:** @page指令路由
```razor
<!-- 基本路由配置 -->
@page "/"

<h1>Home Page</h1>
<p>Welcome to the home page!</p>

<!-- 带参数的路由 -->
@page "/user/{id}"

<h1>User Profile</h1>
<p>User ID: @UserId</p>

@code {
    [Parameter]
    public string Id { get; set; }

    private string UserId => Id;
}

<!-- 多个路由 -->
@page "/products"
@page "/items"

<h1>Products</h1>
<p>Welcome to the products page!</p>
```

**Pattern 2:** 路由参数提取
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

    protected override void OnParametersSet()
    {
        // 处理路由参数变化
        LoadProduct(Id);
    }

    private void LoadProduct(int id)
    {
        // 加载产品数据
    }
}

<!-- 可选参数和约束 -->
@page "/search/{query?}"
@page "/search/{query}/{page:int=1}"

<h1>Search Results</h1>
<p>Query: @Query</p>
<p>Page: @Page</p>

@code {
    [Parameter]
    public string Query { get; set; }

    [Parameter]
    public int Page { get; set; } = 1;
}
```

**Pattern 3:** 导航管理(NavigationManager)
```razor
<!-- NavigationManager使用 -->
@inject NavigationManager NavigationManager

<h1>Navigation Example</h1>

<button @onclick="NavigateToHome">Go Home</button>
<button @onclick="NavigateToProduct">Go to Product 1</button>
<button @onclick="NavigateToSearch">Search for Blazor</button>
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

    private void NavigateToSearch()
    {
        NavigationManager.NavigateTo("/search/Blazor");
    }

    private void GoBack()
    {
        NavigationManager.NavigateTo("/", forceLoad: true);
    }

    // 监听导航事件
    protected override void OnInitialized()
    {
        NavigationManager.LocationChanged += OnLocationChanged;
    }

    private void OnLocationChanged(object sender, LocationChangedEventArgs e)
    {
        Console.WriteLine($"Navigation from {e.PreviousLocation} to {e.Location}");
    }

    public void Dispose()
    {
        NavigationManager.LocationChanged -= OnLocationChanged;
    }
}
```

**Pattern 4:** 查询字符串处理
```razor
<!-- 查询字符串处理 -->
@inject NavigationManager NavigationManager

<h1>Search Page</h1>

<form @onsubmit="HandleSearch">
    <input type="text" @bind="searchQuery" placeholder="Search..." />
    <select @bind="category">
        <option value="">All Categories</option>
        <option value="electronics">Electronics</option>
        <option value="clothing">Clothing</option>
        <option value="books">Books</option>
    </select>
    <button type="submit">Search</button>
</form>

<p>Current search: @CurrentSearch</p>

@code {
    private string searchQuery;
    private string category;
    private string CurrentSearch => $"Query: {searchQuery}, Category: {category}";

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
        category = queryParams["category"] ?? string.Empty;
    }

    private void HandleSearch(EventArgs e)
    {
        // 构建查询字符串
        var query = new Dictionary<string, string>();
        if (!string.IsNullOrEmpty(searchQuery))
        {
            query["q"] = searchQuery;
        }
        if (!string.IsNullOrEmpty(category))
        {
            query["category"] = category;
        }

        var queryString = string.Join("&", query.Select(kvp => $"{kvp.Key}={Uri.EscapeDataString(kvp.Value)}"));
        var url = $"/search?{queryString}";

        NavigationManager.NavigateTo(url);
    }
}
```

**Pattern 5:** 路由守卫
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
    private bool isLoading = true;

    protected override async Task OnInitializedAsync()
    {
        await CheckAuthentication();
    }

    private async Task CheckAuthentication()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        isAuthenticated = authState.User.Identity.IsAuthenticated;
        isLoading = false;

        if (!isAuthenticated)
        {
            // 重定向到登录页面
            NavigationManager.NavigateTo($"/login?returnUrl={Uri.EscapeDataString(NavigationManager.Uri)}");
        }
    }

    private void NavigateToLogin()
    {
        NavigationManager.NavigateTo($"/login?returnUrl={Uri.EscapeDataString(NavigationManager.Uri)}");
    }
}

<!-- 登录页面 -->
@page "/login"
@inject NavigationManager NavigationManager
@inject AuthenticationStateProvider AuthenticationStateProvider

<h1>Login Page</h1>

<form @onsubmit="HandleLogin">
    <input type="text" @bind="username" placeholder="Username" />
    <input type="password" @bind="password" placeholder="Password" />
    <button type="submit">Login</button>
</form>

@code {
    private string username;
    private string password;
    private string returnUrl;

    protected override void OnInitialized()
    {
        // 从查询字符串获取返回URL
        var uri = new Uri(NavigationManager.Uri);
        var queryParams = System.Web.HttpUtility.ParseQueryString(uri.Query);
        returnUrl = queryParams["returnUrl"] ?? "/";
    }

    private async Task HandleLogin(EventArgs e)
    {
        // 模拟登录
        // 实际应用中，这里应该调用认证服务
        
        // 登录成功后导航回原页面
        NavigationManager.NavigateTo(returnUrl);
    }
}
```

## Examples

### Example 1: 配置@page指令路由
- Input: 需要为页面配置路由
- Steps:
  1. 在组件顶部添加@page指令
  2. 配置路由路径
  3. 可选：添加多个路由
  4. 测试路由访问
- Expected output / acceptance: 路由正确配置和访问

### Example 2: 提取路由参数
- Input: 需要从路由中提取参数
- Steps:
  1. 在@page指令中定义参数
  2. 添加[Parameter]属性的对应字段
  3. 在组件中使用参数
  4. 测试带参数的路由
- Expected output / acceptance: 路由参数正确提取和使用

### Example 3: 使用NavigationManager进行导航
- Input: 需要在组件中实现导航
- Steps:
  1. 注入NavigationManager
  2. 调用NavigateTo方法进行导航
  3. 可选：监听LocationChanged事件
  4. 测试导航功能
- Expected output / acceptance: 导航正确实现和响应

## References

- `references/index.md`: Blazor路由配置最佳实践导航
- `references/page-directive.md`: @page指令路由配置指南
- `references/route-parameters.md`: 路由参数提取指南
- `references/navigation-manager.md`: NavigationManager使用指南
- `references/query-strings.md`: 查询字符串处理指南
- `references/route-guards.md`: 路由守卫实现指南

## Maintenance

- Sources: Blazor官方文档和行业最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责具体业务逻辑的实现