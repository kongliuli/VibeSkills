# 前端组件脚手架技能

## 技能描述

前端组件脚手架技能专注于为前端项目提供专业的组件开发框架，确保组件的一致性、可维护性和可扩展性，提高开发效率和代码质量。

**核心功能：**
- React/Vue组件模板
- Props/State结构设计
- 事件处理实现
- 组件样式配置
- 默认属性设置

## 目录结构

```
skills/JavaScript/component-scaffold/
├── SKILL.md          # 技术规范和代码模式
├── README.md         # 本文件，用户指南
├── references/       # 参考文档
├── scripts/          # 脚本文件
└── assets/           # 资源文件
```

## 快速开始

### 1. React组件创建

#### 步骤1：创建组件文件

```bash
# 创建组件目录
mkdir -p src/components/Button

# 创建组件文件
touch src/components/Button/Button.jsx
touch src/components/Button/Button.css
```

#### 步骤2：编写组件代码

```jsx
// src/components/Button/Button.jsx
import React from 'react';
import PropTypes from 'prop-types';
import './Button.css';

const Button = ({ 
  children, 
  variant = 'primary', 
  size = 'medium', 
  disabled = false, 
  onClick, 
  className = '' 
}) => {
  return (
    <button
      className={`button button--${variant} button--${size} ${className}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
};

Button.propTypes = {
  children: PropTypes.node.isRequired,
  variant: PropTypes.oneOf(['primary', 'secondary', 'danger', 'success']),
  size: PropTypes.oneOf(['small', 'medium', 'large']),
  disabled: PropTypes.bool,
  onClick: PropTypes.func,
  className: PropTypes.string,
};

Button.defaultProps = {
  variant: 'primary',
  size: 'medium',
  disabled: false,
  onClick: null,
  className: '',
};

export default Button;
```

```css
/* src/components/Button/Button.css */
.button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  outline: none;
}

.button:hover:not(:disabled) {
  transform: translateY(-1px);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.button:active:not(:disabled) {
  transform: translateY(0);
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

/* 变体样式 */
.button--primary {
  background-color: #2196f3;
  color: white;
}

.button--primary:hover:not(:disabled) {
  background-color: #1976d2;
}

.button--secondary {
  background-color: #f5f5f5;
  color: #333;
  border: 1px solid #ddd;
}

.button--secondary:hover:not(:disabled) {
  background-color: #e0e0e0;
}

.button--danger {
  background-color: #f44336;
  color: white;
}

.button--danger:hover:not(:disabled) {
  background-color: #d32f2f;
}

.button--success {
  background-color: #4caf50;
  color: white;
}

.button--success:hover:not(:disabled) {
  background-color: #388e3c;
}

/* 尺寸样式 */
.button--small {
  padding: 4px 8px;
  font-size: 12px;
}

.button--medium {
  padding: 8px 16px;
  font-size: 14px;
}

.button--large {
  padding: 12px 24px;
  font-size: 16px;
}
```

#### 步骤3：使用组件

```jsx
import React from 'react';
import Button from './components/Button/Button';

const App = () => {
  const handleClick = () => {
    console.log('Button clicked!');
  };

  return (
    <div>
      <Button variant="primary" onClick={handleClick}>
        Primary Button
      </Button>
      <Button variant="secondary" size="small">
        Secondary Button
      </Button>
      <Button variant="danger" disabled>
        Disabled Button
      </Button>
      <Button variant="success" size="large">
        Large Button
      </Button>
    </div>
  );
};

export default App;
```

### 2. Vue组件创建

#### 步骤1：创建组件文件

```bash
# 创建组件目录
mkdir -p src/components/Button

# 创建组件文件
touch src/components/Button/Button.vue
```

#### 步骤2：编写组件代码

```vue
<!-- src/components/Button/Button.vue -->
<template>
  <button
    class="button"
    :class="[
      `button--${variant}`,
      `button--${size}`,
      className
    ]"
    :disabled="disabled"
    @click="handleClick"
  >
    <slot></slot>
  </button>
</template>

<script>
export default {
  name: 'Button',
  
  props: {
    variant: {
      type: String,
      default: 'primary',
      validator: (value) => [
        'primary', 'secondary', 'danger', 'success'
      ].includes(value)
    },
    size: {
      type: String,
      default: 'medium',
      validator: (value) => [
        'small', 'medium', 'large'
      ].includes(value)
    },
    disabled: {
      type: Boolean,
      default: false
    },
    onClick: {
      type: Function,
      default: null
    },
    className: {
      type: String,
      default: ''
    }
  },
  
  methods: {
    handleClick(event) {
      if (!this.disabled && this.onClick) {
        this.onClick(event);
      }
    }
  }
};
</script>

<style scoped>
.button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  outline: none;
}

.button:hover:not(:disabled) {
  transform: translateY(-1px);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.button:active:not(:disabled) {
  transform: translateY(0);
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

/* 变体样式 */
.button--primary {
  background-color: #2196f3;
  color: white;
}

.button--primary:hover:not(:disabled) {
  background-color: #1976d2;
}

.button--secondary {
  background-color: #f5f5f5;
  color: #333;
  border: 1px solid #ddd;
}

.button--secondary:hover:not(:disabled) {
  background-color: #e0e0e0;
}

.button--danger {
  background-color: #f44336;
  color: white;
}

.button--danger:hover:not(:disabled) {
  background-color: #d32f2f;
}

.button--success {
  background-color: #4caf50;
  color: white;
}

.button--success:hover:not(:disabled) {
  background-color: #388e3c;
}

/* 尺寸样式 */
.button--small {
  padding: 4px 8px;
  font-size: 12px;
}

.button--medium {
  padding: 8px 16px;
  font-size: 14px;
}

.button--large {
  padding: 12px 24px;
  font-size: 16px;
}
</style>
```

#### 步骤3：使用组件

```vue
<!-- src/App.vue -->
<template>
  <div>
    <Button variant="primary" @onClick="handleClick">
      Primary Button
    </Button>
    <Button variant="secondary" size="small">
      Secondary Button
    </Button>
    <Button variant="danger" :disabled="true">
      Disabled Button
    </Button>
    <Button variant="success" size="large">
      Large Button
    </Button>
  </div>
</template>

<script>
import Button from './components/Button/Button';

export default {
  name: 'App',
  components: {
    Button
  },
  methods: {
    handleClick() {
      console.log('Button clicked!');
    }
  }
};
</script>
```

## 核心功能

### 1. Props设计

#### React Props设计

```jsx
import React from 'react';
import PropTypes from 'prop-types';

const Component = ({
  // 基础属性
  id,
  className = '',
  style = {},
  // 功能属性
  title = 'Default Title',
  items = [],
  // 状态属性
  disabled = false,
  loading = false,
  // 回调函数
  onItemClick,
  onSubmit,
  // 配置属性
  config = {
    showHeader: true,
    showFooter: false,
  },
}) => {
  // 组件逻辑
  return (
    <div id={id} className={className} style={style}>
      {/* 组件内容 */}
    </div>
  );
};

Component.propTypes = {
  id: PropTypes.string,
  className: PropTypes.string,
  style: PropTypes.object,
  title: PropTypes.string,
  items: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),
      name: PropTypes.string.isRequired,
    })
  ),
  disabled: PropTypes.bool,
  loading: PropTypes.bool,
  onItemClick: PropTypes.func,
  onSubmit: PropTypes.func,
  config: PropTypes.shape({
    showHeader: PropTypes.bool,
    showFooter: PropTypes.bool,
  }),
};

Component.defaultProps = {
  className: '',
  style: {},
  title: 'Default Title',
  items: [],
  disabled: false,
  loading: false,
  config: {
    showHeader: true,
    showFooter: false,
  },
};

export default Component;
```

#### Vue Props设计

```vue
<template>
  <div :id="id" :class="className" :style="style">
    <!-- 组件内容 -->
  </div>
</template>

<script>
export default {
  name: 'Component',
  props: {
    id: {
      type: String,
      default: null
    },
    className: {
      type: String,
      default: ''
    },
    style: {
      type: Object,
      default: () => ({})
    },
    title: {
      type: String,
      default: 'Default Title'
    },
    items: {
      type: Array,
      default: () => []
    },
    disabled: {
      type: Boolean,
      default: false
    },
    loading: {
      type: Boolean,
      default: false
    },
    onItemClick: {
      type: Function,
      default: null
    },
    onSubmit: {
      type: Function,
      default: null
    },
    config: {
      type: Object,
      default: () => ({
        showHeader: true,
        showFooter: false,
      })
    }
  }
};
</script>
```

### 2. State管理

#### React State管理

```jsx
import React, { useState, useReducer, useContext } from 'react';

// 使用useState
const Counter = () => {
  const [count, setCount] = useState(0);
  const [isIncrementing, setIsIncrementing] = useState(false);

  const handleIncrement = () => {
    setIsIncrementing(true);
    setTimeout(() => {
      setCount(prev => prev + 1);
      setIsIncrementing(false);
    }, 1000);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement} disabled={isIncrementing}>
        {isIncrementing ? 'Incrementing...' : 'Increment'}
      </button>
    </div>
  );
};

// 使用useReducer
const initialState = {
  count: 0,
  isLoading: false,
};

function counterReducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { ...state, count: state.count + 1 };
    case 'decrement':
      return { ...state, count: state.count - 1 };
    case 'setLoading':
      return { ...state, isLoading: action.payload };
    default:
      return state;
  }
}

const ReducerCounter = () => {
  const [state, dispatch] = useReducer(counterReducer, initialState);

  const handleIncrement = () => {
    dispatch({ type: 'setLoading', payload: true });
    setTimeout(() => {
      dispatch({ type: 'increment' });
      dispatch({ type: 'setLoading', payload: false });
    }, 1000);
  };

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={handleIncrement} disabled={state.isLoading}>
        {state.isLoading ? 'Loading...' : 'Increment'}
      </button>
    </div>
  );
};

export { Counter, ReducerCounter };
```

#### Vue State管理

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="handleIncrement" :disabled="isIncrementing">
      {{ isIncrementing ? 'Incrementing...' : 'Increment' }}
    </button>
  </div>
</template>

<script>
export default {
  name: 'Counter',
  data() {
    return {
      count: 0,
      isIncrementing: false,
    };
  },
  methods: {
    handleIncrement() {
      this.isIncrementing = true;
      setTimeout(() => {
        this.count++;
        this.isIncrementing = false;
      }, 1000);
    },
  },
};
</script>
```

### 3. 事件处理

#### React事件处理

```jsx
import React, { useState, useCallback } from 'react';

const EventComponent = () => {
  const [value, setValue] = useState('');
  const [isSubmitting, setIsSubmitting] = useState(false);

  // 使用useCallback优化性能
  const handleChange = useCallback((e) => {
    setValue(e.target.value);
  }, []);

  const handleSubmit = useCallback(async (e) => {
    e.preventDefault();
    
    if (isSubmitting) return;
    
    setIsSubmitting(true);
    
    try {
      // 模拟API调用
      await new Promise(resolve => setTimeout(resolve, 1000));
      console.log('Submitted value:', value);
    } catch (error) {
      console.error('Submit error:', error);
    } finally {
      setIsSubmitting(false);
    }
  }, [value, isSubmitting]);

  const handleReset = useCallback(() => {
    setValue('');
  }, []);

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={value}
        onChange={handleChange}
        placeholder="Enter value"
      />
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Submitting...' : 'Submit'}
      </button>
      <button type="button" onClick={handleReset}>
        Reset
      </button>
    </form>
  );
};

export default EventComponent;
```

#### Vue事件处理

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <input
      type="text"
      v-model="value"
      @input="handleInput"
      placeholder="Enter value"
    />
    <button type="submit" :disabled="isSubmitting">
      {{ isSubmitting ? 'Submitting...' : 'Submit' }}
    </button>
    <button type="button" @click="handleReset">
      Reset
    </button>
  </form>
</template>

<script>
export default {
  name: 'EventComponent',
  data() {
    return {
      value: '',
      isSubmitting: false,
    };
  },
  methods: {
    handleInput(e) {
      this.value = e.target.value;
    },
    async handleSubmit() {
      if (this.isSubmitting) return;
      
      this.isSubmitting = true;
      
      try {
        // 模拟API调用
        await new Promise(resolve => setTimeout(resolve, 1000));
        console.log('Submitted value:', this.value);
      } catch (error) {
        console.error('Submit error:', error);
      } finally {
        this.isSubmitting = false;
      }
    },
    handleReset() {
      this.value = '';
    },
  },
};
</script>
```

### 4. 组件样式

#### CSS Modules

```css
/* Component.module.css */
.container {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  background-color: #fff;
}

.title {
  margin-top: 0;
  margin-bottom: 16px;
  font-size: 18px;
  font-weight: 600;
}

.content {
  margin-bottom: 16px;
}

.button {
  padding: 8px 16px;
  background-color: #2196f3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.button:hover {
  background-color: #1976d2;
}

.button:disabled {
  background-color: #bdbdbd;
  cursor: not-allowed;
}
```

```jsx
import React from 'react';
import styles from './Component.module.css';

const Component = () => {
  return (
    <div className={styles.container}>
      <h2 className={styles.title}>Component Title</h2>
      <div className={styles.content}>
        Component content
      </div>
      <button className={styles.button}>
        Action
      </button>
    </div>
  );
};

export default Component;
```

#### Styled Components

```jsx
import React from 'react';
import styled from 'styled-components';

const Container = styled.div`
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  background-color: #fff;
`;

const Title = styled.h2`
  margin-top: 0;
  margin-bottom: 16px;
  font-size: 18px;
  font-weight: 600;
`;

const Content = styled.div`
  margin-bottom: 16px;
`;

const Button = styled.button`
  padding: 8px 16px;
  background-color: #2196f3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s ease;

  &:hover {
    background-color: #1976d2;
  }

  &:disabled {
    background-color: #bdbdbd;
    cursor: not-allowed;
  }
`;

const StyledComponent = () => {
  return (
    <Container>
      <Title>Component Title</Title>
      <Content>
        Component content
      </Content>
      <Button>
        Action
      </Button>
    </Container>
  );
};

export default StyledComponent;
```

#### Tailwind CSS

```jsx
import React from 'react';

const TailwindComponent = () => {
  return (
    <div className="border border-gray-200 rounded-lg p-4 bg-white">
      <h2 className="text-xl font-semibold mb-4">Component Title</h2>
      <div className="mb-4">
        Component content
      </div>
      <button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors">
        Action
      </button>
    </div>
  );
};

export default TailwindComponent;
```

## 最佳实践

### 1. 组件设计

- **单一职责**：每个组件只负责一个功能
- **可复用性**：设计通用组件，避免硬编码
- **可扩展性**：通过Props和配置支持不同场景
- **可测试性**：组件逻辑清晰，易于测试

### 2. 命名规范

- **组件名**：使用 PascalCase（如 `Button`, `UserProfile`）
- **Props名**：使用 camelCase（如 `isDisabled`, `onClick`）
- **类名**：使用 kebab-case（如 `button-primary`, `form-field`）
- **文件结构**：按功能组织，一个组件一个目录

### 3. 性能优化

- **React**：使用 `useMemo`, `useCallback`, `React.memo` 优化渲染
- **Vue**：使用 `v-memo`, `computed`, `watch` 优化性能
- **样式**：使用 CSS Modules, Styled Components 或 Tailwind CSS
- **渲染**：避免不必要的渲染，合理使用条件渲染

### 4. 可访问性

- **语义化HTML**：使用正确的HTML标签
- **ARIA属性**：添加适当的ARIA属性
- **键盘导航**：支持键盘操作
- **屏幕阅读器**：确保内容对屏幕阅读器友好

### 5. 文档

- **代码注释**：添加清晰的代码注释
- **组件文档**：提供组件使用说明
- **示例**：包含使用示例
- **README**：为每个组件创建README文件

## 常见问题

### 1. 组件性能问题

**问题：** 组件渲染性能不佳

**解决方案：**
- 使用React的 `useMemo`, `useCallback`, `React.memo`
- 使用Vue的 `v-memo`, `computed`
- 避免在渲染过程中创建新对象和函数
- 使用虚拟化处理长列表

### 2. Props传递问题

**问题：** Props传递层级过深

**解决方案：**
- 使用React的Context API
- 使用Vue的provide/inject
- 使用状态管理库（如Redux, Vuex, Pinia）
- 重构组件结构，减少层级

### 3. 样式冲突问题

**问题：** 组件样式与全局样式冲突

**解决方案：**
- 使用CSS Modules
- 使用Styled Components
- 使用Vue的scoped样式
- 使用Tailwind CSS的前缀

### 4. 组件测试问题

**问题：** 组件难以测试

**解决方案：**
- 设计纯函数组件
- 使用Jest, React Testing Library, Vue Test Utils
- 模拟外部依赖
- 编写单元测试和集成测试

### 5. 代码维护问题

**问题：** 组件代码难以维护

**解决方案：**
- 遵循编码规范
- 拆分复杂组件
- 使用TypeScript添加类型
- 编写清晰的文档和注释

## 示例应用

### 完整的React组件库

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.jsx
│   │   ├── Button.css
│   │   └── README.md
│   ├── Card/
│   │   ├── Card.jsx
│   │   ├── Card.css
│   │   └── README.md
│   ├── Form/
│   │   ├── Form.jsx
│   │   ├── FormField.jsx
│   │   ├── Form.css
│   │   └── README.md
│   └── Modal/
│       ├── Modal.jsx
│       ├── Modal.css
│       └── README.md
├── utils/
│   ├── validation.js
│   └── formatting.js
└── App.jsx
```

### 完整的Vue组件库

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.vue
│   │   └── README.md
│   ├── Card/
│   │   ├── Card.vue
│   │   └── README.md
│   ├── Form/
│   │   ├── Form.vue
│   │   ├── FormField.vue
│   │   └── README.md
│   └── Modal/
│       ├── Modal.vue
│       └── README.md
├── utils/
│   ├── validation.js
│   └── formatting.js
└── App.vue
```

## 总结

前端组件脚手架技能为前端项目提供了一套完整的组件开发解决方案，通过标准化的组件结构、合理的Props设计、高效的状态管理和灵活的样式配置，帮助开发者构建更加可靠、可维护的前端应用。

使用本技能，您可以：
- 提高组件开发效率
- 确保组件的一致性和可维护性
- 优化组件性能
- 提升代码质量
- 构建专业的前端应用

希望本技能能够帮助您构建更好的前端组件！