# 前端API调用生成器技能

## 技能描述

前端API调用生成器技能专注于为前端项目提供专业的API调用和管理能力，确保API调用的一致性、可靠性和类型安全性，提高开发效率和代码质量。

**核心功能：**
- Fetch/Axios封装
- 错误统一处理
- 重试逻辑实现
- 请求拦截器配置
- 响应类型定义

## 目录结构

```
skills/JavaScript/api-call-generator/
├── SKILL.md          # 技术规范和代码模式
├── README.md         # 本文件，用户指南
├── references/       # 参考文档
├── scripts/          # 脚本文件
└── assets/           # 资源文件
```

## 快速开始

### 1. 安装依赖

如果使用Axios，需要先安装：

```bash
npm install axios
```

### 2. 创建API服务实例

#### 使用Fetch API：

```javascript
// api.js
import { FetchApi } from './path/to/fetch-api';

const api = new FetchApi('https://api.example.com');

export default api;
```

#### 使用Axios：

```javascript
// api.js
import { AxiosApi } from './path/to/axios-api';

const api = new AxiosApi('https://api.example.com');

export default api;
```

### 3. 基本用法

#### GET请求

```javascript
import api from './api';

// 获取用户列表
api.get('/users')
  .then(data => console.log('Users:', data))
  .catch(error => console.error('Error:', error));

// 获取单个用户
api.get('/users/1')
  .then(data => console.log('User:', data))
  .catch(error => console.error('Error:', error));
```

#### POST请求

```javascript
import api from './api';

// 创建用户
api.post('/users', {
  name: 'John Doe',
  email: 'john@example.com'
})
  .then(data => console.log('Created user:', data))
  .catch(error => console.error('Error:', error));
```

#### PUT请求

```javascript
import api from './api';

// 更新用户
api.put('/users/1', {
  name: 'John Doe Updated'
})
  .then(data => console.log('Updated user:', data))
  .catch(error => console.error('Error:', error));
```

#### DELETE请求

```javascript
import api from './api';

// 删除用户
api.delete('/users/1')
  .then(data => console.log('Deleted user:', data))
  .catch(error => console.error('Error:', error));
```

## 高级用法

### 1. 配置请求拦截器

#### Axios拦截器

```javascript
// 在创建AxiosApi实例后添加
api.instance.interceptors.request.use(
  (config) => {
    // 添加认证令牌
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    // 添加其他自定义逻辑
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);
```

### 2. 配置错误处理

```javascript
// 使用ErrorHandler处理错误
import { ErrorHandler } from './path/to/error-handler';

api.request('/users')
  .then(data => console.log('Users:', data))
  .catch(error => {
    const standardizedError = ErrorHandler.handleApiError(error);
    ErrorHandler.showError(standardizedError);
    // 其他错误处理逻辑
  });
```

### 3. 实现重试逻辑

```javascript
// 创建带重试逻辑的API实例
import { RetryApi } from './path/to/retry-api';

const apiWithRetry = new RetryApi('https://api.example.com', {
  maxAttempts: 3,      // 最大重试次数
  delay: 1000,         // 初始延迟（毫秒）
  backoffFactor: 2,    // 延迟增长因子
  retryableStatusCodes: [429, 500, 502, 503, 504]  // 可重试的状态码
});

// 使用带重试逻辑的API
apiWithRetry.get('/users')
  .then(data => console.log('Users:', data))
  .catch(error => console.error('Error after retries:', error));
```

### 4. TypeScript类型定义

```typescript
// types.ts
export interface ApiResponse<T = any> {
  success: boolean;
  data: T;
  message?: string;
  error?: string;
}

export interface User {
  id: number;
  name: string;
  email: string;
  createdAt: string;
  updatedAt: string;
}

// 使用类型
import { TypedApiService } from './path/to/typed-api-service';

const api = new TypedApiService('https://api.example.com');

// 类型安全的API调用
api.getUsers().then(response => {
  if (response.success) {
    // response.data 类型为 User[]
    response.data.forEach(user => {
      console.log(user.name); // IDE提供类型提示
    });
  }
});
```

## 最佳实践

### 1. 统一API配置

- 创建一个中心化的API配置文件，管理所有API端点和配置
- 使用环境变量存储API基础URL，便于不同环境切换

### 2. 错误处理

- 实现全局错误处理，统一处理API错误
- 区分网络错误、服务器错误和业务错误
- 为用户提供友好的错误提示

### 3. 性能优化

- 使用请求缓存减少重复请求
- 实现批量请求合并，减少HTTP请求次数
- 合理设置超时时间，避免请求长时间阻塞

### 4. 安全性

- 不在前端存储敏感信息
- 使用HTTPS协议
- 实现请求签名验证（如果需要）
- 合理处理认证令牌

### 5. 可维护性

- 模块化API服务，按功能划分
- 使用TypeScript提高类型安全性
- 编写清晰的文档和注释
- 实现单元测试

## 常见问题

### 1. API调用超时

**问题：** API调用经常超时

**解决方案：**
- 检查网络连接
- 调整超时时间设置
- 实现重试逻辑
- 考虑使用防抖和节流

### 2. 认证令牌管理

**问题：** 如何安全管理认证令牌

**解决方案：**
- 使用localStorage或sessionStorage存储令牌
- 在请求拦截器中添加令牌
- 实现令牌刷新机制
- 处理令牌过期的情况

### 3. CORS跨域问题

**问题：** 遇到CORS跨域错误

**解决方案：**
- 后端配置CORS头
- 使用代理服务器
- 考虑使用JSONP（仅适用于GET请求）

### 4. 类型定义不一致

**问题：** TypeScript类型定义与实际API响应不一致

**解决方案：**
- 仔细检查API文档
- 使用工具自动生成类型定义
- 实现运行时类型检查

## 示例应用

### 完整的API服务示例

```javascript
// src/services/api.js
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

// 创建API实例
const api = new ApiClient({
  baseUrl: process.env.REACT_APP_API_BASE_URL || 'https://api.example.com',
  timeout: 10000,
  retry: {
    maxAttempts: 3,
    delay: 1000,
    backoffFactor: 2,
  },
});

export default api;

// API端点封装
export const userApi = {
  getUsers: () => api.get('/users'),
  getUser: (id) => api.get(`/users/${id}`),
  createUser: (user) => api.post('/users', user),
  updateUser: (id, user) => api.put(`/users/${id}`, user),
  deleteUser: (id) => api.delete(`/users/${id}`),
};

export const productApi = {
  getProducts: () => api.get('/products'),
  getProduct: (id) => api.get(`/products/${id}`),
  createProduct: (product) => api.post('/products', product),
  updateProduct: (id, product) => api.put(`/products/${id}`, product),
  deleteProduct: (id) => api.delete(`/products/${id}`),
};
```

### 使用示例

```javascript
// src/components/UserList.js
import React, { useState, useEffect } from 'react';
import { userApi } from '../services/api';

const UserList = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        setLoading(true);
        const response = await userApi.getUsers();
        setUsers(response);
        setError(null);
      } catch (err) {
        setError(err.message);
        setUsers([]);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  const handleDelete = async (userId) => {
    try {
      await userApi.deleteUser(userId);
      setUsers(users.filter(user => user.id !== userId));
    } catch (err) {
      setError(err.message);
    }
  };

  if (loading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error: {error}</div>;
  }

  return (
    <div>
      <h1>User List</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>
            {user.name} ({user.email})
            <button onClick={() => handleDelete(user.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```

## 总结

前端API调用生成器技能为前端项目提供了一套完整的API调用解决方案，通过标准化的API调用流程、统一的错误处理、智能的重试逻辑和类型安全的响应处理，帮助开发者构建更加可靠、可维护的前端应用。

使用本技能，您可以：
- 减少API调用相关的重复代码
- 提高API调用的可靠性和稳定性
- 增强代码的类型安全性
- 提高开发效率和代码质量

希望本技能能够帮助您构建更好的前端应用！