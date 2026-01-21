# 前端表单验证器技能

## 技能描述

前端表单验证器技能专注于为前端项目提供专业的表单验证能力，确保表单数据的准确性、完整性和一致性，提高用户体验和数据质量。

**核心功能：**
- 校验规则定义
- 实时验证逻辑
- 错误提示组件
- 异步验证处理
- 表单状态管理

## 目录结构

```
skills/JavaScript/form-validator/
├── SKILL.md          # 技术规范和代码模式
├── README.md         # 本文件，用户指南
├── references/       # 参考文档
├── scripts/          # 脚本文件
└── assets/           # 资源文件
```

## 快速开始

### 1. 基本用法

#### 步骤1：定义验证规则

```javascript
import { ValidationRules } from './path/to/validation-rules';

const rules = {
  name: [
    (value) => ValidationRules.required(value, 'Name is required'),
    (value) => ValidationRules.minLength(value, 2, 'Name must be at least 2 characters'),
    (value) => ValidationRules.maxLength(value, 50, 'Name must be at most 50 characters'),
  ],
  email: [
    (value) => ValidationRules.required(value, 'Email is required'),
    (value) => ValidationRules.email('Invalid email format'),
  ],
  password: [
    (value) => ValidationRules.required(value, 'Password is required'),
    (value) => ValidationRules.minLength(value, 8, 'Password must be at least 8 characters'),
  ],
};
```

#### 步骤2：创建表单验证器实例

```javascript
import { FormValidator } from './path/to/form-validator';

const validator = new FormValidator(rules);
```

#### 步骤3：绑定事件

```javascript
const form = document.getElementById('myForm');
const inputs = form.querySelectorAll('input');

// 绑定输入事件
inputs.forEach(input => {
  input.addEventListener('input', async (e) => {
    const { name, value } = e.target;
    await validator.validateField(name, value);
    validator.touchField(name);
    updateErrors();
  });

  // 绑定失焦事件
  input.addEventListener('blur', async (e) => {
    const { name, value } = e.target;
    await validator.validateField(name, value);
    validator.touchField(name);
    updateErrors();
  });
});

// 绑定提交事件
form.addEventListener('submit', async (e) => {
  e.preventDefault();
  
  const formData = new FormData(form);
  const values = Object.fromEntries(formData.entries());
  
  const isValid = await validator.validateAll(values);
  
  if (isValid) {
    console.log('Form submitted:', values);
  } else {
    console.log('Form errors:', validator.errors);
  }
});

// 更新错误显示
function updateErrors() {
  // 清除所有错误
  document.querySelectorAll('.error-message').forEach(el => el.remove());
  
  // 显示新错误
  Object.entries(validator.errors).forEach(([field, error]) => {
    const input = form[field];
    if (input && validator.touched[field]) {
      const errorElement = document.createElement('div');
      errorElement.className = 'error-message';
      errorElement.textContent = error;
      input.parentNode.insertBefore(errorElement, input.nextSibling);
    }
  });
}
```

## 高级用法

### 1. 异步验证

```javascript
import { AsyncValidation } from './path/to/async-validation';

const rules = {
  username: [
    (value) => ValidationRules.required(value, 'Username is required'),
    (value) => ValidationRules.minLength(value, 3, 'Username must be at least 3 characters'),
    // 异步验证用户名是否已存在
    (value) => AsyncValidation.usernameExists(value),
  ],
  email: [
    (value) => ValidationRules.required(value, 'Email is required'),
    (value) => ValidationRules.email('Invalid email format'),
    // 异步验证邮箱是否已注册
    (value) => AsyncValidation.emailRegistered(value),
  ],
};
```

### 2. 自定义验证规则

```javascript
// 自定义验证规则
const customRules = {
  // 验证手机号码
  phone: (value, message = 'Invalid phone number') => {
    if (value && !/^1[3-9]\d{9}$/.test(value)) {
      return message;
    }
    return null;
  },
  
  // 验证身份证号
  idCard: (value, message = 'Invalid ID card number') => {
    if (value && !/^[1-9]\d{5}(18|19|20)\d{2}(0[1-9]|1[0-2])(0[1-9]|[12]\d|3[01])\d{3}[\dXx]$/.test(value)) {
      return message;
    }
    return null;
  },
  
  // 验证密码强度
  passwordStrength: (value, message = 'Password must contain at least one uppercase letter, one lowercase letter and one number') => {
    if (value && (!/[A-Z]/.test(value) || !/[a-z]/.test(value) || !/\d/.test(value))) {
      return message;
    }
    return null;
  },
};

// 使用自定义规则
const rules = {
  phone: [
    (value) => ValidationRules.required(value, 'Phone is required'),
    (value) => customRules.phone(value),
  ],
  idCard: [
    (value) => ValidationRules.required(value, 'ID card is required'),
    (value) => customRules.idCard(value),
  ],
  password: [
    (value) => ValidationRules.required(value, 'Password is required'),
    (value) => ValidationRules.minLength(value, 8, 'Password must be at least 8 characters'),
    (value) => customRules.passwordStrength(value),
  ],
};
```

### 3. 表单状态管理

```javascript
import { FormState } from './path/to/form-state';

// 创建表单状态实例
const formState = new FormState({
  name: '',
  email: '',
  password: '',
});

// 订阅状态变化
const unsubscribe = formState.subscribe((state) => {
  console.log('Form state:', {
    values: state.values,
    errors: state.errors,
    touched: state.touched,
    isValid: state.isValid(),
    isDirty: state.isDirty,
  });
  
  // 更新UI
  updateFormUI(state);
});

// 处理输入变化
const handleInput = (field, value) => {
  formState.setValue(field, value);
};

// 处理表单提交
const handleSubmit = async () => {
  formState.setSubmitting(true);
  
  try {
    // 验证表单
    const isValid = await validateForm(formState.values);
    
    if (isValid) {
      // 提交表单
      await submitForm(formState.values);
      console.log('Form submitted successfully');
      // 重置表单
      formState.reset();
    } else {
      console.log('Form has errors');
    }
  } catch (error) {
    console.error('Submit error:', error);
  } finally {
    formState.setSubmitting(false);
  }
};

// 清理订阅
// unsubscribe();
```

### 4. 错误提示组件

```javascript
import { ErrorMessage } from './path/to/error-message';

// 显示错误消息
ErrorMessage.show('username', 'Username is required', {
  className: 'error-message',
  id: 'username-error',
});

// 隐藏错误消息
ErrorMessage.hide('username');

// 清空所有错误消息
ErrorMessage.clearAll();
```

## 框架集成

### 1. React集成

```jsx
import React, { useState, useEffect } from 'react';

const LoginForm = () => {
  const [values, setValues] = useState({
    email: '',
    password: '',
  });
  
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  // 验证规则
  const validate = (name, value) => {
    let error = null;
    
    switch (name) {
      case 'email':
        if (!value) {
          error = 'Email is required';
        } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
          error = 'Invalid email format';
        }
        break;
      case 'password':
        if (!value) {
          error = 'Password is required';
        } else if (value.length < 8) {
          error = 'Password must be at least 8 characters';
        }
        break;
      default:
        break;
    }
    
    return error;
  };

  // 处理输入变化
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };

  // 处理失焦事件
  const handleBlur = (e) => {
    const { name, value } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    const error = validate(name, value);
    setErrors(prev => ({ ...prev, [name]: error }));
  };

  // 处理表单提交
  const handleSubmit = (e) => {
    e.preventDefault();
    
    // 验证所有字段
    const newErrors = {};
    Object.keys(values).forEach(name => {
      const error = validate(name, values[name]);
      if (error) {
        newErrors[name] = error;
      }
    });
    
    setErrors(newErrors);
    setTouched(Object.keys(values).reduce((acc, key) => {
      acc[key] = true;
      return acc;
    }, {}));
    
    if (Object.keys(newErrors).length === 0) {
      setIsSubmitting(true);
      
      // 模拟表单提交
      setTimeout(() => {
        console.log('Form submitted:', values);
        setIsSubmitting(false);
      }, 1000);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          type="email"
          id="email"
          name="email"
          value={values.email}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.email && errors.email && (
          <div className="error-message">{errors.email}</div>
        )}
      </div>
      
      <div>
        <label htmlFor="password">Password</label>
        <input
          type="password"
          id="password"
          name="password"
          value={values.password}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.password && errors.password && (
          <div className="error-message">{errors.password}</div>
        )}
      </div>
      
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Logging in...' : 'Log in'}
      </button>
    </form>
  );
};

export default LoginForm;
```

### 2. Vue集成

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label for="email">Email</label>
      <input
        type="email"
        id="email"
        v-model="values.email"
        @blur="handleBlur('email')"
      />
      <div v-if="touched.email && errors.email" class="error-message">
        {{ errors.email }}
      </div>
    </div>
    
    <div>
      <label for="password">Password</label>
      <input
        type="password"
        id="password"
        v-model="values.password"
        @blur="handleBlur('password')"
      />
      <div v-if="touched.password && errors.password" class="error-message">
        {{ errors.password }}
      </div>
    </div>
    
    <button type="submit" :disabled="isSubmitting">
      {{ isSubmitting ? 'Logging in...' : 'Log in' }}
    </button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      values: {
        email: '',
        password: '',
      },
      errors: {},
      touched: {},
      isSubmitting: false,
    };
  },
  methods: {
    validate(name, value) {
      let error = null;
      
      switch (name) {
        case 'email':
          if (!value) {
            error = 'Email is required';
          } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
            error = 'Invalid email format';
          }
          break;
        case 'password':
          if (!value) {
            error = 'Password is required';
          } else if (value.length < 8) {
            error = 'Password must be at least 8 characters';
          }
          break;
        default:
          break;
      }
      
      return error;
    },
    handleBlur(name) {
      this.touched[name] = true;
      this.errors[name] = this.validate(name, this.values[name]);
    },
    handleSubmit() {
      // 验证所有字段
      const newErrors = {};
      Object.keys(this.values).forEach(name => {
        newErrors[name] = this.validate(name, this.values[name]);
      });
      
      this.errors = newErrors;
      this.touched = Object.keys(this.values).reduce((acc, key) => {
        acc[key] = true;
        return acc;
      }, {});
      
      if (Object.values(newErrors).every(error => !error)) {
        this.isSubmitting = true;
        
        // 模拟表单提交
        setTimeout(() => {
          console.log('Form submitted:', this.values);
          this.isSubmitting = false;
        }, 1000);
      }
    },
  },
};
</script>
```

## 最佳实践

### 1. 验证时机

- **实时验证**：用户输入时立即验证，提供即时反馈
- **失焦验证**：用户离开字段时验证，减少干扰
- **提交验证**：表单提交时验证所有字段，确保数据完整性

### 2. 错误提示

- **位置**：错误消息应靠近对应的表单字段
- **样式**：使用明显的颜色和图标标识错误
- **内容**：错误消息应清晰、具体、友好
- **可访问性**：确保错误消息对屏幕阅读器友好

### 3. 性能优化

- **防抖**：对实时验证使用防抖，减少验证次数
- **节流**：对异步验证使用节流，避免过多的API调用
- **缓存**：缓存验证结果，避免重复验证

### 4. 安全性

- **前端验证**：作为用户体验的一部分，提供即时反馈
- **后端验证**：作为安全保障，验证所有数据
- **防止注入**：对用户输入进行适当的转义和验证

### 5. 用户体验

- **进度指示**：显示表单填写进度
- **自动填充**：提供智能自动填充建议
- **格式化**：自动格式化输入（如电话号码、日期）
- **帮助文本**：为复杂字段提供帮助信息

## 常见问题

### 1. 异步验证状态管理

**问题：** 异步验证时用户体验不佳

**解决方案：**
- 显示加载指示器
- 禁用相关字段或按钮
- 使用节流减少API调用
- 提供清晰的反馈信息

### 2. 复杂表单验证

**问题：** 复杂表单的验证逻辑难以维护

**解决方案：**
- 将验证逻辑拆分为小函数
- 使用验证库（如Yup、VeeValidate）
- 实现表单状态管理
- 编写单元测试

### 3. 跨字段验证

**问题：** 需要验证多个字段之间的关系

**解决方案：**
- 使用函数式验证规则
- 传递多个字段的值进行验证
- 在表单提交时进行综合验证

### 4. 可访问性

**问题：** 错误消息对屏幕阅读器不友好

**解决方案：**
- 使用ARIA属性（role="alert", aria-live）
- 确保错误消息与字段关联（aria-describedby）
- 提供键盘导航支持
- 测试屏幕阅读器兼容性

### 5. 国际化

**问题：** 需要支持多语言错误消息

**解决方案：**
- 使用i18n库管理错误消息
- 为验证规则提供多语言支持
- 动态切换错误消息语言

## 示例应用

### 完整的注册表单

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Registration Form</title>
  <style>
    .form-group {
      margin-bottom: 15px;
    }
    label {
      display: block;
      margin-bottom: 5px;
      font-weight: bold;
    }
    input {
      width: 100%;
      padding: 8px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    input.error {
      border-color: #f44336;
    }
    .error-message {
      color: #f44336;
      font-size: 12px;
      margin-top: 5px;
    }
    button {
      padding: 10px 15px;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    button:hover {
      background-color: #45a049;
    }
    button:disabled {
      background-color: #cccccc;
      cursor: not-allowed;
    }
  </style>
</head>
<body>
  <h1>Registration Form</h1>
  <form id="registerForm">
    <div class="form-group">
      <label for="username">Username</label>
      <input type="text" id="username" name="username" />
    </div>

    <div class="form-group">
      <label for="email">Email</label>
      <input type="email" id="email" name="email" />
    </div>

    <div class="form-group">
      <label for="password">Password</label>
      <input type="password" id="password" name="password" />
    </div>

    <div class="form-group">
      <label for="confirmPassword">Confirm Password</label>
      <input type="password" id="confirmPassword" name="confirmPassword" />
    </div>

    <button type="submit" id="submitBtn">Register</button>
  </form>

  <script>
    // 简化版验证实现
    class ValidationRules {
      static required(value, message = 'This field is required') {
        if (value === null || value === undefined || value === '') {
          return message;
        }
        return null;
      }

      static minLength(value, min, message = `Minimum length is ${min}`) {
        if (value && value.length < min) {
          return message;
        }
        return null;
      }

      static email(value, message = 'Invalid email format') {
        if (value && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
          return message;
        }
        return null;
      }

      static confirm(value, otherValue, message = 'Values do not match') {
        if (value !== otherValue) {
          return message;
        }
        return null;
      }
    }

    class FormValidator {
      constructor(rules = {}) {
        this.rules = rules;
        this.errors = {};
        this.touched = {};
        this.isSubmitting = false;
      }

      async validateField(field, value) {
        if (!this.rules[field]) {
          return null;
        }

        for (const rule of this.rules[field]) {
          let error;
          
          if (typeof rule === 'function') {
            error = await rule(value);
          } else {
            error = rule;
          }

          if (error) {
            this.errors[field] = error;
            return error;
          }
        }

        delete this.errors[field];
        return null;
      }

      async validateAll(values) {
        const promises = Object.keys(this.rules).map(field => 
          this.validateField(field, values[field])
        );

        await Promise.all(promises);
        return Object.keys(this.errors).length === 0;
      }

      touchField(field) {
        this.touched[field] = true;
      }

      touchAll() {
        Object.keys(this.rules).forEach(field => {
          this.touched[field] = true;
        });
      }
    }

    class ErrorMessage {
      static show(fieldId, message) {
        this.hide(fieldId);
        
        const field = document.getElementById(fieldId);
        if (field) {
          const errorElement = document.createElement('div');
          errorElement.className = 'error-message';
          errorElement.textContent = message;
          errorElement.setAttribute('role', 'alert');
          
          field.parentNode.insertBefore(errorElement, field.nextSibling);
          field.classList.add('error');
        }
      }

      static hide(fieldId) {
        const field = document.getElementById(fieldId);
        if (field) {
          field.classList.remove('error');
          
          const errorElement = field.nextElementSibling;
          if (errorElement && errorElement.classList.contains('error-message')) {
            errorElement.remove();
          }
        }
      }

      static clearAll() {
        document.querySelectorAll('.error-message').forEach(el => el.remove());
        document.querySelectorAll('input.error').forEach(el => el.classList.remove('error'));
      }
    }

    // 初始化表单
    const form = document.getElementById('registerForm');
    const submitBtn = document.getElementById('submitBtn');
    const passwordInput = document.getElementById('password');
    
    const validator = new FormValidator({
      username: [
        (value) => ValidationRules.required(value, 'Username is required'),
        (value) => ValidationRules.minLength(value, 3, 'Username must be at least 3 characters'),
      ],
      email: [
        (value) => ValidationRules.required(value, 'Email is required'),
        (value) => ValidationRules.email('Invalid email format'),
      ],
      password: [
        (value) => ValidationRules.required(value, 'Password is required'),
        (value) => ValidationRules.minLength(value, 8, 'Password must be at least 8 characters'),
      ],
      confirmPassword: [
        (value) => ValidationRules.required(value, 'Please confirm your password'),
        (value) => ValidationRules.confirm(value, passwordInput.value, 'Passwords do not match'),
      ],
    });

    // 绑定事件
    form.querySelectorAll('input').forEach(input => {
      input.addEventListener('input', async (e) => {
        const { name, value, id } = e.target;
        await validator.validateField(name, value);
        
        if (validator.errors[name]) {
          ErrorMessage.show(id, validator.errors[name]);
        } else {
          ErrorMessage.hide(id);
        }
      });

      input.addEventListener('blur', async (e) => {
        const { name, value, id } = e.target;
        validator.touchField(name);
        await validator.validateField(name, value);
        
        if (validator.errors[name]) {
          ErrorMessage.show(id, validator.errors[name]);
        } else {
          ErrorMessage.hide(id);
        }
      });
    });

    // 密码确认字段特殊处理
    passwordInput.addEventListener('input', () => {
      const confirmPasswordInput = document.getElementById('confirmPassword');
      if (confirmPasswordInput.value) {
        const error = ValidationRules.confirm(
          confirmPasswordInput.value, 
          passwordInput.value, 
          'Passwords do not match'
        );
        
        if (error) {
          validator.errors.confirmPassword = error;
          ErrorMessage.show('confirmPassword', error);
        } else {
          delete validator.errors.confirmPassword;
          ErrorMessage.hide('confirmPassword');
        }
      }
    });

    // 提交事件
    form.addEventListener('submit', async (e) => {
      e.preventDefault();
      
      submitBtn.disabled = true;
      submitBtn.textContent = 'Submitting...';
      
      const formData = new FormData(form);
      const values = Object.fromEntries(formData.entries());
      
      validator.touchAll();
      const isValid = await validator.validateAll(values);
      
      if (isValid) {
        // 模拟提交
        setTimeout(() => {
          console.log('Form submitted:', values);
          alert('Registration successful!');
          form.reset();
          ErrorMessage.clearAll();
          submitBtn.disabled = false;
          submitBtn.textContent = 'Register';
        }, 1000);
      } else {
        // 显示所有错误
        Object.entries(validator.errors).forEach(([field, error]) => {
          const fieldId = form[field].id;
          ErrorMessage.show(fieldId, error);
        });
        
        submitBtn.disabled = false;
        submitBtn.textContent = 'Register';
      }
    });
  </script>
</body>
</html>
```

## 总结

前端表单验证器技能为前端项目提供了一套完整的表单验证解决方案，通过标准化的验证规则、实时的验证反馈、友好的错误提示和高效的状态管理，帮助开发者构建更加用户友好、数据可靠的表单。

使用本技能，您可以：
- 提高表单数据的准确性和完整性
- 改善用户体验，提供即时反馈
- 减少后端错误处理的负担
- 构建更加可维护、可扩展的表单系统

希望本技能能够帮助您构建更好的前端表单！