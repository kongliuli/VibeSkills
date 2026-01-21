---
name: frontend-api-call-generator
description: "前端API调用生成器技能：负责Fetch/Axios封装、错误统一处理、重试逻辑、请求拦截器、响应类型定义。使用时需要配置API基础URL、实现请求拦截器、配置错误处理、设置重试逻辑、定义响应类型。"
---

# frontend-api-call-generator Skill

为前端项目提供专业的API调用和管理能力，确保API调用的一致性、可靠性和类型安全性，提高开发效率和代码质量。

## When to Use This Skill

Trigger when any of these applies:
- 需要封装Fetch或Axios进行API调用
- 需要统一处理API错误
- 需要实现API请求重试逻辑
- 需要配置请求和响应拦截器
- 需要定义API响应类型
- 需要优化现有API调用代码

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代后端API设计
- 不处理API的性能优化（仅调用层面）
- 不负责API的安全性（如认证令牌管理）

## Quick Reference

### Common Patterns

**Pattern 1:** Fetch API封装
```javascript
// Fetch API封装
class FetchApi {
  constructor(baseUrl = '') {
    this.baseUrl = baseUrl;
    this.defaultHeaders = {
      'Content-Type': 'application/json',
    };
  }

  /**
   * 发送请求
   * @param {string} endpoint - API端点
   * @param {Object} options - 请求选项
   * @returns {Promise<any>} - 响应数据
   */
  async request(endpoint, options = {}) {
    const url = `${this.baseUrl}${endpoint}`;
    const config = {
      ...options,
      headers: {
        ...this.defaultHeaders,
        ...options.headers,
      },
    };

    try {
      const response = await fetch(url, config);
      
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const data = await response.json();
      return data;
    } catch (error) {
      console.error('API request error:', error);
      throw error;
    }
  }

  /**
   * GET请求
   * @param {string} endpoint - API端点
   * @param {Object} options - 请求选项
   * @returns {Promise<any>} - 响应数据
   */
  get(endpoint, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'GET',
    });
  }

  /**
   * POST请求
   * @param {string} endpoint - API端点
   * @param {Object} data - 请求数据
   * @param {Object} options - 请求选项
   * @returns {Promise<any>} - 响应数据
   */
  post(endpoint, data, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'POST',
      body: JSON.stringify(data),
    });
  }

  /**
   * PUT请求
   * @param {string} endpoint - API端点
   * @param {Object} data - 请求数据
   * @param {Object} options - 请求选项
   * @returns {Promise<any>} - 响应数据
   */
  put(endpoint, data, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'PUT',
      body: JSON.stringify(data),
    });
  }

  /**
   * DELETE请求
   * @param {string} endpoint - API端点
   * @param {Object} options - 请求选项
   * @returns {Promise<any>} - 响应数据
   */
  delete(endpoint, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'DELETE',
    });
  }
}

// 使用示例
const api = new FetchApi('https://api.example.com');

// GET请求
api.get('/users')
  .then(data => console.log('Users:', data))
  .catch(error => console.error('Error:', error));

// POST请求
api.post('/users', { name: 'John', email: 'john@example.com' })
  .then(data => console.log('Created user:', data))
  .catch(error => console.error('Error:', error));
```

**Pattern 2:** Axios封装
```javascript
// Axios封装
import axios from 'axios';

class AxiosApi {
  constructor(baseUrl = '') {
    this.instance = axios.create({
      baseURL: baseUrl,
      headers: {
        'Content-Type': 'application/json',
      },
      timeout: 10000, // 10秒超时
    });

    this.setupInterceptors();
  }

  /**
   * 设置拦截器
   */
  setupInterceptors() {
    // 请求拦截器
    this.instance.interceptors.request.use(
      (config) => {
        // 可以在这里添加认证令牌等
        // const token = localStorage.getItem('token');
        // if (token) {
        //   config.headers.Authorization = `Bearer ${token}`;
        // }
        return config;
      },
      (error) => {
        return Promise.reject(error);
      }
    );

    // 响应拦截器
    this.instance.interceptors.response.use(
      (response) => {
        return response.data;
      },
      (error) => {
        return this.handleError(error);
      }
    );
  }

  /**
   * 处理错误
   * @param {Error} error - 错误对象
   * @returns {Promise<never>} - 拒绝的Promise
   */
  handleError(error) {
    if (error.response) {
      // 服务器返回错误状态码
      console.error('API Error:', {
        status: error.response.status,
        data: error.response.data,
        headers: error.response.headers,
      });
    } else if (error.request) {
      // 请求已发送但没有收到响应
      console.error('API Error: No response received', error.request);
    } else {
      // 请求配置出错
      console.error('API Error:', error.message);
    }
    return Promise.reject(error);
  }

  /**
   * GET请求
   * @param {string} url - API端点
   * @param {Object} config - 请求配置
   * @returns {Promise<any>} - 响应数据
   */
  get(url, config = {}) {
    return this.instance.get(url, config);
  }

  /**
   * POST请求
   * @param {string} url - API端点
   * @param {Object} data - 请求数据
   * @param {Object} config - 请求配置
   * @returns {Promise<any>} - 响应数据
   */
  post(url, data, config = {}) {
    return this.instance.post(url, data, config);
  }

  /**
   * PUT请求
   * @param {string} url - API端点
   * @param {Object} data - 请求数据
   * @param {Object} config - 请求配置
   * @returns {Promise<any>} - 响应数据
   */
  put(url, data, config = {}) {
    return this.instance.put(url, data, config);
  }

  /**
   * DELETE请求
   * @param {string} url - API端点
   * @param {Object} config - 请求配置
   * @returns {Promise<any>} - 响应数据
   */
  delete(url, config = {}) {
    return this.instance.delete(url, config);
  }
}

// 使用示例
const api = new AxiosApi('https://api.example.com');

// GET请求
api.get('/users')
  .then(data => console.log('Users:', data))
  .catch(error => console.error('Error:', error));

// POST请求
api.post('/users', { name: 'John', email: 'john@example.com' })
  .then(data => console.log('Created user:', data))
  .catch(error => console.error('Error:', error));
```

**Pattern 3:** 错误统一处理
```javascript
// 错误统一处理
class ErrorHandler {
  /**
   * 处理API错误
   * @param {Error} error - 错误对象
   * @returns {Object} - 标准化的错误对象
   */
  static handleApiError(error) {
    const standardizedError = {
      message: 'Unknown error',
      status: null,
      details: null,
    };

    if (error.response) {
      // 服务器返回错误状态码
      standardizedError.status = error.response.status;
      
      if (error.response.data) {
        standardizedError.message = error.response.data.message || 'Server error';
        standardizedError.details = error.response.data;
      } else {
        standardizedError.message = `Server error: ${error.response.status}`;
      }
    } else if (error.request) {
      // 请求已发送但没有收到响应
      standardizedError.message = 'No response from server';
      standardizedError.status = 0;
    } else {
      // 请求配置出错
      standardizedError.message = error.message;
      standardizedError.status = 0;
    }

    // 可以根据状态码进行特定处理
    switch (standardizedError.status) {
      case 401:
        // 未授权，可以重定向到登录页
        console.error('Unauthorized');
        // window.location.href = '/login';
        break;
      case 403:
        // 禁止访问
        console.error('Forbidden');
        break;
      case 404:
        // 资源不存在
        console.error('Resource not found');
        break;
      case 500:
        // 服务器内部错误
        console.error('Internal server error');
        break;
      default:
        break;
    }

    return standardizedError;
  }

  /**
   * 显示错误消息
   * @param {Object} error - 标准化的错误对象
   */
  static showError(error) {
    // 这里可以集成UI库显示错误消息
    alert(error.message);
  }
}

// 使用示例
class ApiService {
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
  }

  async request(endpoint, options = {}) {
    try {
      const response = await fetch(`${this.baseUrl}${endpoint}`, options);
      
      if (!response.ok) {
        const errorData = await response.json().catch(() => ({}));
        const error = new Error(errorData.message || 'API Error');
        error.response = { status: response.status, data: errorData };
        throw error;
      }

      return await response.json();
    } catch (error) {
      const standardizedError = ErrorHandler.handleApiError(error);
      ErrorHandler.showError(standardizedError);
      throw standardizedError;
    }
  }
}

// 使用示例
const api = new ApiService('https://api.example.com');

api.request('/users')
  .then(data => console.log('Users:', data))
  .catch(error => console.error('Handled error:', error));
```

**Pattern 4:** 重试逻辑
```javascript
// 重试逻辑
class RetryApi {
  constructor(baseUrl = '', retryConfig = {}) {
    this.baseUrl = baseUrl;
    this.retryConfig = {
      maxAttempts: 3,
      delay: 1000,
      backoffFactor: 2,
      retryableStatusCodes: [429, 500, 502, 503, 504],
      ...retryConfig,
    };
  }

  /**
   * 发送请求
   * @param {string} endpoint - API端点
   * @param {Object} options - 请求选项
   * @param {number} attempt - 当前尝试次数
   * @returns {Promise<any>} - 响应数据
   */
  async request(endpoint, options = {}, attempt = 1) {
    const url = `${this.baseUrl}${endpoint}`;
    
    try {
      const response = await fetch(url, options);
      
      if (!response.ok) {
        // 检查是否需要重试
        if (
          this.retryConfig.retryableStatusCodes.includes(response.status) &&
          attempt < this.retryConfig.maxAttempts
        ) {
          // 计算重试延迟
          const delay = this.retryConfig.delay * Math.pow(this.retryConfig.backoffFactor, attempt - 1);
          
          console.log(`Retrying request (${attempt}/${this.retryConfig.maxAttempts})...`);
          
          // 延迟后重试
          await new Promise(resolve => setTimeout(resolve, delay));
          return this.request(endpoint, options, attempt + 1);
        }
        
        // 不需要重试或达到最大尝试次数
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      
      return await response.json();
    } catch (error) {
      // 网络错误也可以重试
      if (
        !error.response && // 网络错误
        attempt < this.retryConfig.maxAttempts
      ) {
        const delay = this.retryConfig.delay * Math.pow(this.retryConfig.backoffFactor, attempt - 1);
        
        console.log(`Retrying request after network error (${attempt}/${this.retryConfig.maxAttempts})...`);
        
        await new Promise(resolve => setTimeout(resolve, delay));
        return this.request(endpoint, options, attempt + 1);
      }
      
      throw error;
    }
  }

  /**
   * GET请求
   * @param {string} endpoint - API端点
   * @param {Object} options - 请求选项
   * @returns {Promise<any>} - 响应数据
   */
  get(endpoint, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'GET',
    });
  }

  /**
   * POST请求
   * @param {string} endpoint - API端点
   * @param {Object} data - 请求数据
   * @param {Object} options - 请求选项
   * @returns {Promise<any>} - 响应数据
   */
  post(endpoint, data, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        ...options.headers,
      },
      body: JSON.stringify(data),
    });
  }
}

// 使用示例
const api = new RetryApi('https://api.example.com', {
  maxAttempts: 3,
  delay: 1000,
  backoffFactor: 2,
});

api.get('/users')
  .then(data => console.log('Users:', data))
  .catch(error => console.error('Error after retries:', error));
```

**Pattern 5:** TypeScript响应类型定义
```typescript
// TypeScript响应类型定义
// 基础响应类型
export interface ApiResponse<T = any> {
  success: boolean;
  data: T;
  message?: string;
  error?: string;
}

// 用户类型
export interface User {
  id: number;
  name: string;
  email: string;
  createdAt: string;
  updatedAt: string;
}

// 分页响应类型
export interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
  totalPages: number;
}

// API服务
export class ApiService {
  private baseUrl: string;

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }

  /**
   * 发送请求
   * @param endpoint API端点
   * @param options 请求选项
   * @returns 响应数据
   */
  private async request<T>(endpoint: string, options: RequestInit = {}): Promise<ApiResponse<T>> {
    const url = `${this.baseUrl}${endpoint}`;
    
    try {
      const response = await fetch(url, {
        headers: {
          'Content-Type': 'application/json',
          ...options.headers,
        },
        ...options,
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const data = await response.json();
      return data;
    } catch (error) {
      console.error('API Error:', error);
      throw error;
    }
  }

  /**
   * 获取用户列表
   * @returns 用户列表
   */
  async getUsers(): Promise<ApiResponse<PaginatedResponse<User>>> {
    return this.request<PaginatedResponse<User>>('/users');
  }

  /**
   * 获取单个用户
   * @param id 用户ID
   * @returns 用户信息
   */
  async getUser(id: number): Promise<ApiResponse<User>> {
    return this.request<User>(`/users/${id}`);
  }

  /**
   * 创建用户
   * @param user 用户数据
   * @returns 创建的用户
   */
  async createUser(user: Omit<User, 'id' | 'createdAt' | 'updatedAt'>): Promise<ApiResponse<User>> {
    return this.request<User>('/users', {
      method: 'POST',
      body: JSON.stringify(user),
    });
  }

  /**
   * 更新用户
   * @param id 用户ID
   * @param user 用户数据
   * @returns 更新后的用户
   */
  async updateUser(id: number, user: Partial<User>): Promise<ApiResponse<User>> {
    return this.request<User>(`/users/${id}`, {
      method: 'PUT',
      body: JSON.stringify(user),
    });
  }

  /**
   * 删除用户
   * @param id 用户ID
   * @returns 操作结果
   */
  async deleteUser(id: number): Promise<ApiResponse<{ success: boolean }>> {
    return this.request<{ success: boolean }>(`/users/${id}`, {
      method: 'DELETE',
    });
  }
}

// 使用示例
const api = new ApiService('https://api.example.com');

// 获取用户列表
api.getUsers()
  .then(response => {
    if (response.success) {
      console.log('Users:', response.data.items);
      console.log('Total:', response.data.total);
    } else {
      console.error('Error:', response.error);
    }
  })
  .catch(error => console.error('API Error:', error));

// 创建用户
api.createUser({ name: 'John', email: 'john@example.com' })
  .then(response => {
    if (response.success) {
      console.log('Created user:', response.data);
    } else {
      console.error('Error:', response.error);
    }
  })
  .catch(error => console.error('API Error:', error));
```

## Examples

### Example 1: 完整的API服务

**Scenario:** 创建一个完整的API服务，包含错误处理和重试逻辑。

**Steps:**
1. 初始化API服务
2. 配置基础URL和默认选项
3. 实现请求方法
4. 测试API调用

**Code:**
```javascript
class ApiClient {
  constructor(config = {}) {
    this.config = {
      baseUrl: '',
      timeout: 10000,
      retry: {
        maxAttempts: 3,
        delay: 1000,
        backoffFactor: 2,
        retryableStatusCodes: [429, 500, 502, 503, 504],
      },
      ...config,
    };
  }

  async request(endpoint, options = {}, attempt = 1) {
    const url = `${this.config.baseUrl}${endpoint}`;
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), this.config.timeout);

    try {
      const response = await fetch(url, {
        ...options,
        signal: controller.signal,
        headers: {
          'Content-Type': 'application/json',
          ...options.headers,
        },
      });

      clearTimeout(timeoutId);

      if (!response.ok) {
        const errorData = await response.json().catch(() => ({}));
        const error = new Error(errorData.message || `HTTP error! status: ${response.status}`);
        error.response = { status: response.status, data: errorData };
        
        // 重试逻辑
        if (
          this.config.retry.retryableStatusCodes.includes(response.status) &&
          attempt < this.config.retry.maxAttempts
        ) {
          const delay = this.config.retry.delay * Math.pow(this.config.retry.backoffFactor, attempt - 1);
          console.log(`Retrying request (${attempt}/${this.config.retry.maxAttempts})...`);
          await new Promise(resolve => setTimeout(resolve, delay));
          return this.request(endpoint, options, attempt + 1);
        }
        
        throw error;
      }

      return await response.json();
    } catch (error) {
      clearTimeout(timeoutId);
      
      // 网络错误重试
      if (
        error.name === 'AbortError' || !error.response &&
        attempt < this.config.retry.maxAttempts
      ) {
        const delay = this.config.retry.delay * Math.pow(this.config.retry.backoffFactor, attempt - 1);
        console.log(`Retrying request after error (${attempt}/${this.config.retry.maxAttempts})...`);
        await new Promise(resolve => setTimeout(resolve, delay));
        return this.request(endpoint, options, attempt + 1);
      }
      
      // 错误统一处理
      const standardizedError = {
        message: error.message || 'API Error',
        status: error.response?.status || 0,
        data: error.response?.data || {},
      };
      
      console.error('API Error:', standardizedError);
      throw standardizedError;
    }
  }

  get(endpoint, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'GET',
    });
  }

  post(endpoint, data, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'POST',
      body: JSON.stringify(data),
    });
  }

  put(endpoint, data, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'PUT',
      body: JSON.stringify(data),
    });
  }

  delete(endpoint, options = {}) {
    return this.request(endpoint, {
      ...options,
      method: 'DELETE',
    });
  }
}

// 使用示例
const api = new ApiClient({
  baseUrl: 'https://api.example.com',
  timeout: 10000,
  retry: {
    maxAttempts: 3,
    delay: 1000,
    backoffFactor: 2,
  },
});

// 测试API调用
async function testApi() {
  try {
    // 获取用户列表
    const users = await api.get('/users');
    console.log('Users:', users);

    // 创建用户
    const newUser = await api.post('/users', {
      name: 'John Doe',
      email: 'john@example.com',
    });
    console.log('New User:', newUser);

    // 更新用户
    const updatedUser = await api.put(`/users/${newUser.id}`, {
      name: 'John Doe Updated',
    });
    console.log('Updated User:', updatedUser);

    // 删除用户
    const deleteResult = await api.delete(`/users/${newUser.id}`);
    console.log('Delete Result:', deleteResult);
  } catch (error) {
    console.error('Test Error:', error);
  }
}

testApi();
```

**Expected Output:**
- API调用成功时输出响应数据
- API调用失败时输出标准化的错误信息
- 网络错误时自动重试
- 超时请求被中止

### Example 2: TypeScript API服务

**Scenario:** 创建一个TypeScript API服务，包含完整的类型定义。

**Steps:**
1. 定义类型
2. 创建API服务
3. 实现请求方法
4. 测试API调用

**Code:**
```typescript
// 类型定义
export interface ApiError {
  message: string;
  status: number;
  data?: any;
}

export interface ApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
}

export interface User {
  id: number;
  name: string;
  email: string;
  createdAt: string;
  updatedAt: string;
}

export interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
}

// API服务
export class TypedApiService {
  private baseUrl: string;

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }

  /**
   * 发送请求
   */
  private async request<T>(endpoint: string, options: RequestInit = {}): Promise<ApiResponse<T>> {
    try {
      const response = await fetch(`${this.baseUrl}${endpoint}`, {
        headers: {
          'Content-Type': 'application/json',
          ...options.headers,
        },
        ...options,
      });

      if (!response.ok) {
        const errorData = await response.json().catch(() => ({}));
        const error: ApiError = {
          message: errorData.message || `HTTP error! status: ${response.status}`,
          status: response.status,
          data: errorData,
        };
        throw error;
      }

      const data = await response.json();
      return data;
    } catch (error) {
      console.error('API Error:', error);
      throw error;
    }
  }

  /**
   * 获取用户列表
   */
  async getUsers(): Promise<ApiResponse<User[]>> {
    return this.request<User[]>('/users');
  }

  /**
   * 获取单个用户
   */
  async getUser(id: number): Promise<ApiResponse<User>> {
    return this.request<User>(`/users/${id}`);
  }

  /**
   * 创建用户
   */
  async createUser(user: Omit<User, 'id' | 'createdAt' | 'updatedAt'>): Promise<ApiResponse<User>> {
    return this.request<User>('/users', {
      method: 'POST',
      body: JSON.stringify(user),
    });
  }

  /**
   * 获取产品列表
   */
  async getProducts(): Promise<ApiResponse<Product[]>> {
    return this.request<Product[]>('/products');
  }

  /**
   * 创建产品
   */
  async createProduct(product: Omit<Product, 'id'>): Promise<ApiResponse<Product>> {
    return this.request<Product>('/products', {
      method: 'POST',
      body: JSON.stringify(product),
    });
  }
}

// 使用示例
const api = new TypedApiService('https://api.example.com');

// 测试用户API
async function testUserApi() {
  try {
    // 获取用户列表
    const usersResponse = await api.getUsers();
    if (usersResponse.success) {
      console.log('Users:', usersResponse.data);
      usersResponse.data.forEach(user => {
        console.log(`User: ${user.name} (${user.email})`);
      });
    }

    // 创建用户
    const createResponse = await api.createUser({
      name: 'John Doe',
      email: 'john@example.com',
    });
    if (createResponse.success) {
      console.log('Created User:', createResponse.data);
    }
  } catch (error) {
    console.error('User API Error:', error);
  }
}

// 测试产品API
async function testProductApi() {
  try {
    // 获取产品列表
    const productsResponse = await api.getProducts();
    if (productsResponse.success) {
      console.log('Products:', productsResponse.data);
    }

    // 创建产品
    const createResponse = await api.createProduct({
      name: 'Test Product',
      price: 99.99,
      description: 'A test product',
    });
    if (createResponse.success) {
      console.log('Created Product:', createResponse.data);
    }
  } catch (error) {
    console.error('Product API Error:', error);
  }
}

testUserApi();
testProductApi();
```

**Expected Output:**
- API调用成功时输出类型安全的响应数据
- IDE提供类型提示和自动完成
- 编译时类型检查
- 运行时错误处理

## References

- `references/index.md`: API调用最佳实践导航
- `references/fetch-api.md`: Fetch API封装指南
- `references/axios.md`: Axios封装指南
- `references/error-handling.md`: 错误处理指南
- `references/retry-logic.md`: 重试逻辑指南
- `references/typescript.md`: TypeScript类型定义指南

## Maintenance

- Sources: MDN Fetch API文档、Axios文档、TypeScript文档
- Last updated: 2026-01-21
- Known limits: 不负责API的安全性和性能优化