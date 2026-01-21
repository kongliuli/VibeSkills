---
name: frontend-form-validator
description: "前端表单验证器技能：负责校验规则定义、实时验证逻辑、错误提示组件、异步验证、表单状态管理。使用时需要配置验证规则、实现实时验证、设计错误提示组件、处理异步验证、管理表单状态。"
---

# frontend-form-validator Skill

为前端项目提供专业的表单验证能力，确保表单数据的准确性、完整性和一致性，提高用户体验和数据质量。

## When to Use This Skill

Trigger when any of these applies:
- 需要定义表单验证规则
- 需要实现实时表单验证
- 需要设计错误提示组件
- 需要处理异步表单验证
- 需要管理表单状态
- 需要优化现有表单验证代码

## Not For / Boundaries

- 不负责表单的UI设计
- 不替代后端数据验证
- 不处理表单提交逻辑
- 不负责表单数据的持久化

## Quick Reference

### Common Patterns

**Pattern 1:** 基础验证规则定义
```javascript
// 验证规则定义
class ValidationRules {
  /**
   * 验证必填项
   * @param {string} value - 输入值
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static required(value, message = 'This field is required') {
    if (value === null || value === undefined || value === '') {
      return message;
    }
    if (Array.isArray(value) && value.length === 0) {
      return message;
    }
    return null;
  }

  /**
   * 验证最小长度
   * @param {string} value - 输入值
   * @param {number} min - 最小长度
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static minLength(value, min, message = `Minimum length is ${min}`) {
    if (value && value.length < min) {
      return message;
    }
    return null;
  }

  /**
   * 验证最大长度
   * @param {string} value - 输入值
   * @param {number} max - 最大长度
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static maxLength(value, max, message = `Maximum length is ${max}`) {
    if (value && value.length > max) {
      return message;
    }
    return null;
  }

  /**
   * 验证电子邮件
   * @param {string} value - 输入值
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static email(value, message = 'Invalid email format') {
    if (value) {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!emailRegex.test(value)) {
        return message;
      }
    }
    return null;
  }

  /**
   * 验证手机号码
   * @param {string} value - 输入值
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static phone(value, message = 'Invalid phone number') {
    if (value) {
      const phoneRegex = /^\d{10,11}$/;
      if (!phoneRegex.test(value)) {
        return message;
      }
    }
    return null;
  }

  /**
   * 验证数字
   * @param {string} value - 输入值
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static number(value, message = 'Must be a number') {
    if (value && isNaN(Number(value))) {
      return message;
    }
    return null;
  }

  /**
   * 验证最小值
   * @param {number} value - 输入值
   * @param {number} min - 最小值
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static min(value, min, message = `Minimum value is ${min}`) {
    if (value !== null && value !== undefined && Number(value) < min) {
      return message;
    }
    return null;
  }

  /**
   * 验证最大值
   * @param {number} value - 输入值
   * @param {number} max - 最大值
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static max(value, max, message = `Maximum value is ${max}`) {
    if (value !== null && value !== undefined && Number(value) > max) {
      return message;
    }
    return null;
  }

  /**
   * 验证正则表达式
   * @param {string} value - 输入值
   * @param {RegExp} regex - 正则表达式
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static pattern(value, regex, message = 'Invalid format') {
    if (value && !regex.test(value)) {
      return message;
    }
    return null;
  }

  /**
   * 验证两次输入是否一致
   * @param {string} value - 输入值
   * @param {string} otherValue - 比较值
   * @param {string} message - 错误消息
   * @returns {string|null} - 错误消息或null
   */
  static confirm(value, otherValue, message = 'Values do not match') {
    if (value !== otherValue) {
      return message;
    }
    return null;
  }
}

// 使用示例
const rules = {
  name: [
    ValidationRules.required('Name is required'),
    ValidationRules.minLength(2, 'Name must be at least 2 characters'),
    ValidationRules.maxLength(50, 'Name must be at most 50 characters'),
  ],
  email: [
    ValidationRules.required('Email is required'),
    ValidationRules.email('Invalid email format'),
  ],
  password: [
    ValidationRules.required('Password is required'),
    ValidationRules.minLength(8, 'Password must be at least 8 characters'),
  ],
  confirmPassword: [
    ValidationRules.required('Please confirm your password'),
    (value) => ValidationRules.confirm(value, password, 'Passwords do not match'),
  ],
};
```

**Pattern 2:** 实时验证逻辑
```javascript
// 实时验证逻辑
class FormValidator {
  constructor(rules = {}) {
    this.rules = rules;
    this.errors = {};
    this.touched = {};
    this.isValidating = {};
  }

  /**
   * 验证单个字段
   * @param {string} field - 字段名
   * @param {any} value - 字段值
   * @returns {Promise<string|null>} - 错误消息或null
   */
  async validateField(field, value) {
    if (!this.rules[field]) {
      return null;
    }

    this.isValidating[field] = true;

    try {
      for (const rule of this.rules[field]) {
        let error;
        
        // 处理函数类型的规则
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

      // 验证通过
      delete this.errors[field];
      return null;
    } finally {
      this.isValidating[field] = false;
    }
  }

  /**
   * 验证所有字段
   * @param {Object} values - 表单值
   * @returns {Promise<boolean>} - 是否验证通过
   */
  async validateAll(values) {
    const promises = Object.keys(this.rules).map(field => 
      this.validateField(field, values[field])
    );

    await Promise.all(promises);
    return Object.keys(this.errors).length === 0;
  }

  /**
   * 标记字段为已触摸
   * @param {string} field - 字段名
   */
  touchField(field) {
    this.touched[field] = true;
  }

  /**
   * 标记所有字段为已触摸
   */
  touchAll() {
    Object.keys(this.rules).forEach(field => {
      this.touched[field] = true;
    });
  }

  /**
   * 获取字段的错误消息
   * @param {string} field - 字段名
   * @returns {string|null} - 错误消息或null
   */
  getError(field) {
    if (this.touched[field]) {
      return this.errors[field] || null;
    }
    return null;
  }

  /**
   * 检查表单是否有效
   * @returns {boolean} - 是否有效
   */
  isValid() {
    return Object.keys(this.errors).length === 0;
  }

  /**
   * 重置验证状态
   */
  reset() {
    this.errors = {};
    this.touched = {};
    this.isValidating = {};
  }
}

// 使用示例
const formValidator = new FormValidator({
  name: [
    (value) => ValidationRules.required(value, 'Name is required'),
    (value) => ValidationRules.minLength(value, 2, 'Name must be at least 2 characters'),
  ],
  email: [
    (value) => ValidationRules.required(value, 'Email is required'),
    (value) => ValidationRules.email(value, 'Invalid email format'),
  ],
});

// 实时验证示例
const handleInputChange = async (field, value) => {
  await formValidator.validateField(field, value);
  // 更新表单状态
};

const handleBlur = (field, value) => {
  formValidator.touchField(field);
  formValidator.validateField(field, value);
  // 更新表单状态
};

const handleSubmit = async (values) => {
  formValidator.touchAll();
  const isValid = await formValidator.validateAll(values);
  
  if (isValid) {
    // 提交表单
    console.log('Form submitted:', values);
  } else {
    // 显示错误
    console.log('Form errors:', formValidator.errors);
  }
};
```

**Pattern 3:** 错误提示组件
```javascript
// 错误提示组件
class ErrorMessage {
  /**
   * 创建错误消息元素
   * @param {string} message - 错误消息
   * @param {Object} options - 选项
   * @returns {HTMLElement} - 错误消息元素
   */
  static createElement(message, options = {}) {
    const {
      className = 'error-message',
      id = '',
      ariaLive = 'polite',
    } = options;

    const errorElement = document.createElement('div');
    errorElement.className = className;
    errorElement.textContent = message;
    errorElement.setAttribute('role', 'alert');
    errorElement.setAttribute('aria-live', ariaLive);
    
    if (id) {
      errorElement.id = id;
    }

    return errorElement;
  }

  /**
   * 显示错误消息
   * @param {string} fieldId - 字段ID
   * @param {string} message - 错误消息
   * @param {Object} options - 选项
   */
  static show(fieldId, message, options = {}) {
    // 移除已有的错误消息
    this.hide(fieldId);

    // 创建错误消息元素
    const errorElement = this.createElement(message, options);
    errorElement.dataset.field = fieldId;

    // 找到字段元素
    const fieldElement = document.getElementById(fieldId);
    if (fieldElement) {
      // 在字段后插入错误消息
      fieldElement.parentNode.insertBefore(errorElement, fieldElement.nextSibling);
      
      // 添加错误样式
      fieldElement.classList.add('error');
    }
  }

  /**
   * 隐藏错误消息
   * @param {string} fieldId - 字段ID
   */
  static hide(fieldId) {
    // 查找错误消息元素
    const errorElement = document.querySelector(`[data-field="${fieldId}"]`);
    if (errorElement) {
      errorElement.remove();
    }

    // 移除错误样式
    const fieldElement = document.getElementById(fieldId);
    if (fieldElement) {
      fieldElement.classList.remove('error');
    }
  }

  /**
   * 清空所有错误消息
   */
  static clearAll() {
    const errorElements = document.querySelectorAll('[data-field]');
    errorElements.forEach(element => {
      const fieldId = element.dataset.field;
      this.hide(fieldId);
    });
  }
}

// 使用示例
const formValidator = new FormValidator({
  name: [
    (value) => ValidationRules.required(value, 'Name is required'),
  ],
  email: [
    (value) => ValidationRules.required(value, 'Email is required'),
    (value) => ValidationRules.email(value, 'Invalid email format'),
  ],
});

const handleInput = async (event) => {
  const { name, value, id } = event.target;
  
  const error = await formValidator.validateField(name, value);
  
  if (error) {
    ErrorMessage.show(id, error);
  } else {
    ErrorMessage.hide(id);
  }
};

const handleSubmit = async (event) => {
  event.preventDefault();
  
  const formData = new FormData(event.target);
  const values = Object.fromEntries(formData.entries());
  
  const isValid = await formValidator.validateAll(values);
  
  if (isValid) {
    console.log('Form submitted:', values);
  } else {
    // 显示所有错误
    Object.entries(formValidator.errors).forEach(([field, error]) => {
      const fieldId = event.target[field].id;
      ErrorMessage.show(fieldId, error);
    });
  }
};

// 绑定事件
const form = document.getElementById('myForm');
form.addEventListener('input', handleInput);
form.addEventListener('submit', handleSubmit);
```

**Pattern 4:** 异步验证
```javascript
// 异步验证
class AsyncValidation {
  /**
   * 验证用户名是否已存在
   * @param {string} username - 用户名
   * @param {string} message - 错误消息
   * @returns {Promise<string|null>} - 错误消息或null
   */
  static async usernameExists(username, message = 'Username already exists') {
    if (!username) {
      return null;
    }

    try {
      // 模拟API调用
      await new Promise(resolve => setTimeout(resolve, 500));
      
      // 模拟已存在的用户名
      const existingUsernames = ['admin', 'user', 'test'];
      
      if (existingUsernames.includes(username)) {
        return message;
      }

      return null;
    } catch (error) {
      console.error('Username validation error:', error);
      return 'Error validating username';
    }
  }

  /**
   * 验证电子邮件是否已注册
   * @param {string} email - 电子邮件
   * @param {string} message - 错误消息
   * @returns {Promise<string|null>} - 错误消息或null
   */
  static async emailRegistered(email, message = 'Email already registered') {
    if (!email) {
      return null;
    }

    try {
      // 模拟API调用
      await new Promise(resolve => setTimeout(resolve, 500));
      
      // 模拟已注册的电子邮件
      const registeredEmails = ['admin@example.com', 'user@example.com'];
      
      if (registeredEmails.includes(email)) {
        return message;
      }

      return null;
    } catch (error) {
      console.error('Email validation error:', error);
      return 'Error validating email';
    }
  }

  /**
   * 验证密码强度
   * @param {string} password - 密码
   * @param {string} message - 错误消息
   * @returns {Promise<string|null>} - 错误消息或null
   */
  static async passwordStrength(password, message = 'Password is too weak') {
    if (!password) {
      return null;
    }

    try {
      // 模拟API调用
      await new Promise(resolve => setTimeout(resolve, 300));
      
      // 简单的密码强度检查
      const hasUpperCase = /[A-Z]/.test(password);
      const hasLowerCase = /[a-z]/.test(password);
      const hasNumbers = /\d/.test(password);
      const hasSpecialChars = /[!@#$%^&*(),.?":{}|<>]/.test(password);
      
      const strength = [hasUpperCase, hasLowerCase, hasNumbers, hasSpecialChars]
        .filter(Boolean).length;
      
      if (strength < 3) {
        return message;
      }

      return null;
    } catch (error) {
      console.error('Password strength validation error:', error);
      return 'Error validating password strength';
    }
  }
}

// 使用示例
const formValidator = new FormValidator({
  username: [
    ValidationRules.required('Username is required'),
    ValidationRules.minLength(3, 'Username must be at least 3 characters'),
    (value) => AsyncValidation.usernameExists(value),
  ],
  email: [
    ValidationRules.required('Email is required'),
    ValidationRules.email('Invalid email format'),
    (value) => AsyncValidation.emailRegistered(value),
  ],
  password: [
    ValidationRules.required('Password is required'),
    ValidationRules.minLength(8, 'Password must be at least 8 characters'),
    (value) => AsyncValidation.passwordStrength(value),
  ],
});

// 处理表单提交
const handleSubmit = async (values) => {
  const isValid = await formValidator.validateAll(values);
  
  if (isValid) {
    console.log('Form submitted:', values);
  } else {
    console.log('Form errors:', formValidator.errors);
  }
};
```

**Pattern 5:** 表单状态管理
```javascript
// 表单状态管理
class FormState {
  constructor(initialValues = {}, initialErrors = {}, initialTouched = {}) {
    this.values = { ...initialValues };
    this.errors = { ...initialErrors };
    this.touched = { ...initialTouched };
    this.isSubmitting = false;
    this.isValidating = false;
    this.isDirty = false;
    this.listeners = [];
  }

  /**
   * 更新字段值
   * @param {string} field - 字段名
   * @param {any} value - 字段值
   */
  setValue(field, value) {
    this.values[field] = value;
    this.isDirty = true;
    this.notifyListeners();
  }

  /**
   * 更新多个字段值
   * @param {Object} values - 字段值对象
   */
  setValues(values) {
    this.values = { ...this.values, ...values };
    this.isDirty = true;
    this.notifyListeners();
  }

  /**
   * 设置错误
   * @param {string} field - 字段名
   * @param {string|null} error - 错误消息或null
   */
  setError(field, error) {
    if (error) {
      this.errors[field] = error;
    } else {
      delete this.errors[field];
    }
    this.notifyListeners();
  }

  /**
   * 设置多个错误
   * @param {Object} errors - 错误对象
   */
  setErrors(errors) {
    this.errors = { ...errors };
    this.notifyListeners();
  }

  /**
   * 标记字段为已触摸
   * @param {string} field - 字段名
   */
  setTouched(field) {
    this.touched[field] = true;
    this.notifyListeners();
  }

  /**
   * 标记所有字段为已触摸
   */
  setAllTouched() {
    Object.keys(this.values).forEach(field => {
      this.touched[field] = true;
    });
    this.notifyListeners();
  }

  /**
   * 设置提交状态
   * @param {boolean} isSubmitting - 是否正在提交
   */
  setSubmitting(isSubmitting) {
    this.isSubmitting = isSubmitting;
    this.notifyListeners();
  }

  /**
   * 设置验证状态
   * @param {boolean} isValidating - 是否正在验证
   */
  setValidating(isValidating) {
    this.isValidating = isValidating;
    this.notifyListeners();
  }

  /**
   * 重置表单状态
   * @param {Object} initialValues - 初始值
   */
  reset(initialValues = {}) {
    this.values = { ...initialValues };
    this.errors = {};
    this.touched = {};
    this.isSubmitting = false;
    this.isValidating = false;
    this.isDirty = false;
    this.notifyListeners();
  }

  /**
   * 检查表单是否有效
   * @returns {boolean} - 是否有效
   */
  isValid() {
    return Object.keys(this.errors).length === 0;
  }

  /**
   * 检查字段是否有错误
   * @param {string} field - 字段名
   * @returns {boolean} - 是否有错误
   */
  hasError(field) {
    return this.touched[field] && !!this.errors[field];
  }

  /**
   * 获取字段错误
   * @param {string} field - 字段名
   * @returns {string|null} - 错误消息或null
   */
  getError(field) {
    return this.touched[field] ? this.errors[field] || null : null;
  }

  /**
   * 添加监听器
   * @param {Function} listener - 监听器函数
   * @returns {Function} - 移除监听器的函数
   */
  subscribe(listener) {
    this.listeners.push(listener);
    
    // 立即通知一次当前状态
    listener(this);

    // 返回移除监听器的函数
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }

  /**
   * 通知所有监听器
   */
  notifyListeners() {
    this.listeners.forEach(listener => listener(this));
  }
}

// 使用示例
const formState = new FormState({
  name: '',
  email: '',
  password: '',
});

// 订阅表单状态变化
const unsubscribe = formState.subscribe((state) => {
  console.log('Form state changed:', {
    values: state.values,
    errors: state.errors,
    touched: state.touched,
    isValid: state.isValid(),
    isSubmitting: state.isSubmitting,
    isDirty: state.isDirty,
  });

  // 更新UI
  updateFormUI(state);
});

// 处理输入变化
const handleInputChange = (field, value) => {
  formState.setValue(field, value);
  // 可以在这里触发验证
};

// 处理失焦事件
const handleBlur = async (field, value) => {
  formState.setTouched(field);
  // 触发验证
  const error = await validateField(field, value);
  formState.setError(field, error);
};

// 处理表单提交
const handleSubmit = async (event) => {
  event.preventDefault();
  
  formState.setSubmitting(true);
  formState.setAllTouched();

  try {
    // 验证所有字段
    const errors = await validateAllFields(formState.values);
    formState.setErrors(errors);

    if (formState.isValid()) {
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

// 清理监听器
// unsubscribe();
```

## Examples

### Example 1: 完整的表单验证实现

**Scenario:** 创建一个完整的注册表单，包含实时验证、异步验证和错误提示。

**Steps:**
1. 创建表单HTML
2. 初始化表单验证器
3. 绑定事件监听器
4. 实现验证逻辑
5. 处理表单提交

**Code:**
```html
<!-- form.html -->
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
```

```javascript
// form-validation.js
class RegisterFormValidator {
  constructor() {
    this.formState = new FormState({
      username: '',
      email: '',
      password: '',
      confirmPassword: '',
    });

    this.rules = {
      username: [
        (value) => ValidationRules.required(value, 'Username is required'),
        (value) => ValidationRules.minLength(value, 3, 'Username must be at least 3 characters'),
        (value) => ValidationRules.maxLength(value, 20, 'Username must be at most 20 characters'),
        (value) => AsyncValidation.usernameExists(value),
      ],
      email: [
        (value) => ValidationRules.required(value, 'Email is required'),
        (value) => ValidationRules.email('Invalid email format'),
        (value) => AsyncValidation.emailRegistered(value),
      ],
      password: [
        (value) => ValidationRules.required(value, 'Password is required'),
        (value) => ValidationRules.minLength(value, 8, 'Password must be at least 8 characters'),
        (value) => AsyncValidation.passwordStrength(value),
      ],
      confirmPassword: [
        (value) => ValidationRules.required(value, 'Please confirm your password'),
        (value) => ValidationRules.confirm(value, this.formState.values.password, 'Passwords do not match'),
      ],
    };

    this.bindEvents();
    this.subscribeToStateChanges();
  }

  /**
   * 绑定事件
   */
  bindEvents() {
    const form = document.getElementById('registerForm');
    const inputs = form.querySelectorAll('input');

    // 绑定输入事件
    inputs.forEach(input => {
      input.addEventListener('input', (e) => {
        const { name, value } = e.target;
        this.formState.setValue(name, value);
        this.validateField(name, value);
      });

      // 绑定失焦事件
      input.addEventListener('blur', (e) => {
        const { name, value } = e.target;
        this.formState.setTouched(name);
        this.validateField(name, value);
      });
    });

    // 绑定提交事件
    form.addEventListener('submit', (e) => {
      e.preventDefault();
      this.handleSubmit();
    });
  }

  /**
   * 订阅状态变化
   */
  subscribeToStateChanges() {
    this.formState.subscribe((state) => {
      this.updateUI(state);
    });
  }

  /**
   * 验证字段
   * @param {string} field - 字段名
   * @param {any} value - 字段值
   */
  async validateField(field, value) {
    if (!this.rules[field]) {
      return;
    }

    try {
      for (const rule of this.rules[field]) {
        let error;
        
        if (typeof rule === 'function') {
          error = await rule(value);
        } else {
          error = rule;
        }

        if (error) {
          this.formState.setError(field, error);
          return;
        }
      }

      this.formState.setError(field, null);
    } catch (error) {
      console.error('Validation error:', error);
      this.formState.setError(field, 'Validation error');
    }
  }

  /**
   * 验证所有字段
   */
  async validateAll() {
    const fields = Object.keys(this.rules);
    const validationPromises = fields.map(field => 
      this.validateField(field, this.formState.values[field])
    );

    await Promise.all(validationPromises);
    return this.formState.isValid();
  }

  /**
   * 处理表单提交
   */
  async handleSubmit() {
    this.formState.setSubmitting(true);
    this.formState.setAllTouched();

    try {
      const isValid = await this.validateAll();

      if (isValid) {
        // 模拟表单提交
        await new Promise(resolve => setTimeout(resolve, 1000));
        console.log('Form submitted:', this.formState.values);
        alert('Registration successful!');
        this.formState.reset();
      } else {
        console.log('Form has errors:', this.formState.errors);
      }
    } catch (error) {
      console.error('Submit error:', error);
    } finally {
      this.formState.setSubmitting(false);
    }
  }

  /**
   * 更新UI
   * @param {FormState} state - 表单状态
   */
  updateUI(state) {
    // 更新错误消息
    Object.keys(state.values).forEach(field => {
      const error = state.getError(field);
      const fieldId = field;

      if (error) {
        ErrorMessage.show(fieldId, error);
      } else {
        ErrorMessage.hide(fieldId);
      }
    });

    // 更新提交按钮
    const submitBtn = document.getElementById('submitBtn');
    submitBtn.disabled = state.isSubmitting;
    submitBtn.textContent = state.isSubmitting ? 'Submitting...' : 'Register';
  }
}

// 初始化表单验证
const registerForm = new RegisterFormValidator();
```

**Expected Output:**
- 实时验证表单字段
- 显示错误消息
- 处理异步验证
- 禁用提交按钮当正在提交时
- 提交成功后重置表单

### Example 2: React表单验证

**Scenario:** 在React应用中实现表单验证。

**Steps:**
1. 创建React组件
2. 实现表单状态管理
3. 添加验证逻辑
4. 处理表单提交

**Code:**
```jsx
// RegisterForm.jsx
import React, { useState, useEffect } from 'react';

const RegisterForm = () => {
  // 表单状态
  const [values, setValues] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: '',
  });

  // 错误状态
  const [errors, setErrors] = useState({});

  // 触摸状态
  const [touched, setTouched] = useState({});

  // 提交状态
  const [isSubmitting, setIsSubmitting] = useState(false);

  // 验证状态
  const [isValidating, setIsValidating] = useState({});

  // 验证规则
  const rules = {
    username: [
      (value) => value ? null : 'Username is required',
      (value) => value && value.length < 3 ? 'Username must be at least 3 characters' : null,
      (value) => value && value.length > 20 ? 'Username must be at most 20 characters' : null,
    ],
    email: [
      (value) => value ? null : 'Email is required',
      (value) => value && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) ? 'Invalid email format' : null,
    ],
    password: [
      (value) => value ? null : 'Password is required',
      (value) => value && value.length < 8 ? 'Password must be at least 8 characters' : null,
    ],
    confirmPassword: [
      (value) => value ? null : 'Please confirm your password',
      (value) => value !== values.password ? 'Passwords do not match' : null,
    ],
  };

  // 验证单个字段
  const validateField = (field, value) => {
    if (!rules[field]) {
      return null;
    }

    for (const rule of rules[field]) {
      const error = rule(value);
      if (error) {
        return error;
      }
    }

    return null;
  };

  // 验证所有字段
  const validateAll = () => {
    const newErrors = {};

    Object.keys(rules).forEach(field => {
      const error = validateField(field, values[field]);
      if (error) {
        newErrors[field] = error;
      }
    });

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
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
    
    const error = validateField(name, value);
    setErrors(prev => ({ ...prev, [name]: error }));
  };

  // 处理表单提交
  const handleSubmit = (e) => {
    e.preventDefault();
    
    // 标记所有字段为已触摸
    const allTouched = Object.keys(rules).reduce((acc, field) => {
      acc[field] = true;
      return acc;
    }, {});
    setTouched(allTouched);

    const isValid = validateAll();

    if (isValid) {
      setIsSubmitting(true);
      
      // 模拟表单提交
      setTimeout(() => {
        console.log('Form submitted:', values);
        alert('Registration successful!');
        
        // 重置表单
        setValues({
          username: '',
          email: '',
          password: '',
          confirmPassword: '',
        });
        setErrors({});
        setTouched({});
        setIsSubmitting(false);
      }, 1000);
    }
  };

  // 当password变化时，重新验证confirmPassword
  useEffect(() => {
    if (touched.confirmPassword) {
      const error = validateField('confirmPassword', values.confirmPassword);
      setErrors(prev => ({ ...prev, confirmPassword: error }));
    }
  }, [values.password, touched.confirmPassword]);

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">Username</label>
        <input
          type="text"
          id="username"
          name="username"
          value={values.username}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.username && errors.username && (
          <div className="error-message">{errors.username}</div>
        )}
      </div>

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

      <div>
        <label htmlFor="confirmPassword">Confirm Password</label>
        <input
          type="password"
          id="confirmPassword"
          name="confirmPassword"
          value={values.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.confirmPassword && errors.confirmPassword && (
          <div className="error-message">{errors.confirmPassword}</div>
        )}
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Submitting...' : 'Register'}
      </button>
    </form>
  );
};

export default RegisterForm;
```

**Expected Output:**
- 实时更新表单状态
- 显示字段错误消息
- 处理密码确认验证
- 禁用提交按钮当正在提交时
- 提交成功后重置表单

## References

- `references/index.md`: 表单验证最佳实践导航
- `references/rules.md`: 验证规则指南
- `references/real-time.md`: 实时验证指南
- `references/async.md`: 异步验证指南
- `references/state-management.md`: 表单状态管理指南
- `references/error-handling.md`: 错误处理指南

## Maintenance

- Sources: MDN Web Forms, HTML5 Form Validation, React Forms
- Last updated: 2026-01-21
- Known limits: 不处理复杂的嵌套表单验证