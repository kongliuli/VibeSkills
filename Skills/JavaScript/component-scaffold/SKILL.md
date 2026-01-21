---
name: frontend-component-scaffold
description: "前端组件脚手架技能：负责React/Vue组件模板、Props/State结构设计、事件处理、组件样式、默认属性配置。使用时需要定义组件Props、设计State结构、实现事件处理、配置样式、设置默认属性。"
---

# frontend-component-scaffold Skill

为前端项目提供专业的组件脚手架能力，确保组件的一致性、可维护性和可扩展性，提高开发效率和代码质量。

## When to Use This Skill

Trigger when any of these applies:
- 需要创建新的React或Vue组件
- 需要设计组件的Props和State结构
- 需要实现组件的事件处理逻辑
- 需要为组件配置样式
- 需要设置组件的默认属性
- 需要优化现有组件的结构

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代UI/UX设计
- 不处理组件的性能优化（仅结构层面）
- 不负责组件的测试

## Quick Reference

### Common Patterns

**Pattern 1:** React组件模板
```javascript
// React组件模板
import React, { useState, useEffect, useCallback } from 'react';
import PropTypes from 'prop-types';
import './ComponentName.css';

/**
 * 组件描述
 * @param {Object} props - 组件属性
 * @returns {JSX.Element} - 组件元素
 */
const ComponentName = ({
  // 默认属性
  title = 'Default Title',
  // 必需属性
  items,
  // 可选属性
  className = '',
  onItemClick,
  disabled = false,
  // 样式属性
  style = {},
}) => {
  // 状态管理
  const [activeItem, setActiveItem] = useState(null);
  const [loading, setLoading] = useState(false);

  // 副作用
  useEffect(() => {
    // 初始化逻辑
    console.log('Component mounted');

    // 清理函数
    return () => {
      console.log('Component unmounted');
    };
  }, []); // 空依赖数组，只执行一次

  // 带依赖的副作用
  useEffect(() => {
    // 当items变化时执行
    if (items && items.length > 0) {
      setActiveItem(items[0]);
    }
  }, [items]);

  // 事件处理
  const handleItemClick = useCallback((item) => {
    if (disabled || !onItemClick) return;
    setActiveItem(item);
    onItemClick(item);
  }, [disabled, onItemClick]);

  const handleButtonClick = useCallback(() => {
    setLoading(true);
    // 模拟异步操作
    setTimeout(() => {
      setLoading(false);
    }, 1000);
  }, []);

  // 渲染逻辑
  const renderItems = () => {
    if (!items || items.length === 0) {
      return <div className="component-name-empty">No items available</div>;
    }

    return items.map((item, index) => (
      <div
        key={item.id || index}
        className={`component-name-item ${activeItem === item ? 'active' : ''}`}
        onClick={() => handleItemClick(item)}
      >
        {item.name}
      </div>
    ));
  };

  // 主渲染
  return (
    <div className={`component-name ${className}`} style={style}>
      <h2 className="component-name-title">{title}</h2>
      
      <div className="component-name-content">
        {renderItems()}
      </div>
      
      <button
        className="component-name-button"
        onClick={handleButtonClick}
        disabled={loading || disabled}
      >
        {loading ? 'Loading...' : 'Action'}
      </button>
    </div>
  );
};

// 属性类型定义
ComponentName.propTypes = {
  // 标题
  title: PropTypes.string,
  // 项目列表
  items: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),
      name: PropTypes.string.isRequired,
    })
  ).isRequired,
  // 类名
  className: PropTypes.string,
  // 项目点击事件
  onItemClick: PropTypes.func,
  // 是否禁用
  disabled: PropTypes.bool,
  // 样式
  style: PropTypes.object,
};

// 默认属性
ComponentName.defaultProps = {
  title: 'Default Title',
  className: '',
  onItemClick: null,
  disabled: false,
  style: {},
};

export default ComponentName;
```

**Pattern 2:** Vue组件模板
```javascript
// Vue组件模板
<template>
  <div class="component-name" :class="className" :style="style">
    <h2 class="component-name-title">{{ title }}</h2>
    
    <div class="component-name-content">
      <div v-if="!items || items.length === 0" class="component-name-empty">
        No items available
      </div>
      <div
        v-for="(item, index) in items"
        :key="item.id || index"
        class="component-name-item"
        :class="{ active: activeItem === item }"
        @click="handleItemClick(item)"
      >
        {{ item.name }}
      </div>
    </div>
    
    <button
      class="component-name-button"
      @click="handleButtonClick"
      :disabled="loading || disabled"
    >
      {{ loading ? 'Loading...' : 'Action' }}
    </button>
  </div>
</template>

<script>
export default {
  name: 'ComponentName',
  
  // 属性定义
  props: {
    // 标题
    title: {
      type: String,
      default: 'Default Title',
    },
    // 项目列表
    items: {
      type: Array,
      required: true,
      validator: (value) => {
        return value.every(item => typeof item.name === 'string');
      },
    },
    // 类名
    className: {
      type: String,
      default: '',
    },
    // 项目点击事件
    onItemClick: {
      type: Function,
      default: null,
    },
    // 是否禁用
    disabled: {
      type: Boolean,
      default: false,
    },
    // 样式
    style: {
      type: Object,
      default: () => ({}),
    },
  },
  
  // 数据
  data() {
    return {
      activeItem: null,
      loading: false,
    };
  },
  
  // 计算属性
  computed: {
    // 计算项目数量
    itemCount() {
      return this.items.length;
    },
  },
  
  // 监听器
  watch: {
    // 监听items变化
    items: {
      handler(newItems) {
        if (newItems && newItems.length > 0) {
          this.activeItem = newItems[0];
        }
      },
      immediate: true,
      deep: true,
    },
  },
  
  // 生命周期钩子
  mounted() {
    console.log('Component mounted');
  },
  
  beforeUnmount() {
    console.log('Component before unmount');
  },
  
  // 方法
  methods: {
    // 处理项目点击
    handleItemClick(item) {
      if (this.disabled || !this.onItemClick) return;
      this.activeItem = item;
      this.onItemClick(item);
    },
    
    // 处理按钮点击
    handleButtonClick() {
      this.loading = true;
      // 模拟异步操作
      setTimeout(() => {
        this.loading = false;
      }, 1000);
    },
  },
};
</script>

<style scoped>
.component-name {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  background-color: #fff;
}

.component-name-title {
  margin-top: 0;
  margin-bottom: 16px;
  font-size: 18px;
  font-weight: 600;
}

.component-name-content {
  margin-bottom: 16px;
}

.component-name-item {
  padding: 8px 12px;
  border: 1px solid #eee;
  border-radius: 4px;
  margin-bottom: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.component-name-item:hover {
  background-color: #f5f5f5;
}

.component-name-item.active {
  background-color: #e3f2fd;
  border-color: #2196f3;
}

.component-name-empty {
  padding: 20px;
  text-align: center;
  color: #666;
  background-color: #f9f9f9;
  border-radius: 4px;
}

.component-name-button {
  padding: 8px 16px;
  background-color: #2196f3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.component-name-button:hover:not(:disabled) {
  background-color: #1976d2;
}

.component-name-button:disabled {
  background-color: #bdbdbd;
  cursor: not-allowed;
}
</style>
```

**Pattern 3:** 组件样式配置
```javascript
// 组件样式配置

// 方法1: CSS Modules
// ComponentName.module.css
.component {
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

.item {
  padding: 8px 12px;
  border: 1px solid #eee;
  border-radius: 4px;
  margin-bottom: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.item:hover {
  background-color: #f5f5f5;
}

.item.active {
  background-color: #e3f2fd;
  border-color: #2196f3;
}

// 使用CSS Modules
import React from 'react';
import styles from './ComponentName.module.css';

const ComponentName = ({ items }) => {
  return (
    <div className={styles.component}>
      <h2 className={styles.title}>Component</h2>
      <div className={styles.content}>
        {items.map((item, index) => (
          <div key={index} className={styles.item}>
            {item.name}
          </div>
        ))}
      </div>
    </div>
  );
};

// 方法2: styled-components
import React from 'react';
import styled from 'styled-components';

// 定义样式组件
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

const Item = styled.div`
  padding: 8px 12px;
  border: 1px solid #eee;
  border-radius: 4px;
  margin-bottom: 8px;
  cursor: pointer;
  transition: all 0.2s ease;

  &:hover {
    background-color: #f5f5f5;
  }

  &.active {
    background-color: #e3f2fd;
    border-color: #2196f3;
  }
`;

// 使用styled-components
const StyledComponent = ({ items, activeItem }) => {
  return (
    <Container>
      <Title>Styled Component</Title>
      <Content>
        {items.map((item, index) => (
          <Item 
            key={index} 
            className={activeItem === item ? 'active' : ''}
          >
            {item.name}
          </Item>
        ))}
      </Content>
    </Container>
  );
};

// 方法3: Tailwind CSS
import React from 'react';

const TailwindComponent = ({ items, activeItem, onItemClick }) => {
  return (
    <div className="border border-gray-200 rounded-lg p-4 bg-white">
      <h2 className="text-xl font-semibold mb-4">Tailwind Component</h2>
      <div className="mb-4">
        {items.map((item, index) => (
          <div
            key={index}
            className={`p-2 border rounded mb-2 cursor-pointer transition-all ${activeItem === item ? 'bg-blue-50 border-blue-400' : 'border-gray-200 hover:bg-gray-50'}`}
            onClick={() => onItemClick(item)}
          >
            {item.name}
          </div>
        ))}
      </div>
      <button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors">
        Action
      </button>
    </div>
  );
};
```

**Pattern 4:** 组件Props/State结构设计
```javascript
// 组件Props/State结构设计

// React函数组件
import React, { useState, useReducer } from 'react';

// 复杂状态管理
const initialState = {
  items: [],
  loading: false,
  error: null,
  filter: 'all',
};

function reducer(state, action) {
  switch (action.type) {
    case 'SET_ITEMS':
      return { ...state, items: action.payload };
    case 'SET_LOADING':
      return { ...state, loading: action.payload };
    case 'SET_ERROR':
      return { ...state, error: action.payload };
    case 'SET_FILTER':
      return { ...state, filter: action.payload };
    default:
      return state;
  }
}

const ComplexComponent = ({
  // 基础属性
  title,
  // 配置属性
  config = {
    pagination: true,
    search: true,
    filter: true,
  },
  // 回调函数
  onItemSelect,
  onError,
  // 样式属性
  variant = 'default', // default, compact, expanded
  size = 'medium', // small, medium, large
}) => {
  // 简单状态
  const [selectedItem, setSelectedItem] = useState(null);
  
  // 复杂状态
  const [state, dispatch] = useReducer(reducer, initialState);
  
  // 计算属性
  const filteredItems = state.items.filter(item => {
    if (state.filter === 'all') return true;
    return item.status === state.filter;
  });
  
  // 处理项目选择
  const handleItemSelect = (item) => {
    setSelectedItem(item);
    if (onItemSelect) {
      onItemSelect(item);
    }
  };
  
  // 加载数据
  const loadData = async () => {
    dispatch({ type: 'SET_LOADING', payload: true });
    dispatch({ type: 'SET_ERROR', payload: null });
    
    try {
      // 模拟API调用
      const data = await fetchData();
      dispatch({ type: 'SET_ITEMS', payload: data });
    } catch (error) {
      dispatch({ type: 'SET_ERROR', payload: error.message });
      if (onError) {
        onError(error);
      }
    } finally {
      dispatch({ type: 'SET_LOADING', payload: false });
    }
  };
  
  return (
    <div className={`complex-component variant-${variant} size-${size}`}>
      <h2>{title}</h2>
      
      {config.search && (
        <div className="search-bar">
          {/* 搜索组件 */}
        </div>
      )}
      
      {config.filter && (
        <div className="filter-bar">
          {/* 过滤组件 */}
        </div>
      )}
      
      {state.loading && <div>Loading...</div>}
      {state.error && <div className="error">{state.error}</div>}
      
      <div className="items-list">
        {filteredItems.map(item => (
          <div
            key={item.id}
            className={`item ${selectedItem?.id === item.id ? 'selected' : ''}`}
            onClick={() => handleItemSelect(item)}
          >
            {item.name}
          </div>
        ))}
      </div>
      
      {config.pagination && (
        <div className="pagination">
          {/* 分页组件 */}
        </div>
      )}
    </div>
  );
};

// Vue组件Props/State设计
<template>
  <div class="complex-component" :class="[`variant-${variant}`, `size-${size}`]">
    <h2>{{ title }}</h2>
    
    <div v-if="config.search" class="search-bar">
      <!-- 搜索组件 -->
    </div>
    
    <div v-if="config.filter" class="filter-bar">
      <!-- 过滤组件 -->
    </div>
    
    <div v-if="loading">Loading...</div>
    <div v-if="error" class="error">{{ error }}</div>
    
    <div class="items-list">
      <div
        v-for="item in filteredItems"
        :key="item.id"
        class="item"
        :class="{ selected: selectedItem?.id === item.id }"
        @click="handleItemSelect(item)"
      >
        {{ item.name }}
      </div>
    </div>
    
    <div v-if="config.pagination" class="pagination">
      <!-- 分页组件 -->
    </div>
  </div>
</template>

<script>
export default {
  name: 'ComplexComponent',
  
  props: {
    title: {
      type: String,
      default: 'Complex Component',
    },
    config: {
      type: Object,
      default: () => ({
        pagination: true,
        search: true,
        filter: true,
      }),
    },
    onItemSelect: {
      type: Function,
      default: null,
    },
    onError: {
      type: Function,
      default: null,
    },
    variant: {
      type: String,
      default: 'default',
      validator: (value) => ['default', 'compact', 'expanded'].includes(value),
    },
    size: {
      type: String,
      default: 'medium',
      validator: (value) => ['small', 'medium', 'large'].includes(value),
    },
  },
  
  data() {
    return {
      selectedItem: null,
      items: [],
      loading: false,
      error: null,
      filter: 'all',
    };
  },
  
  computed: {
    filteredItems() {
      if (this.filter === 'all') {
        return this.items;
      }
      return this.items.filter(item => item.status === this.filter);
    },
  },
  
  methods: {
    handleItemSelect(item) {
      this.selectedItem = item;
      if (this.onItemSelect) {
        this.onItemSelect(item);
      }
    },
    
    async loadData() {
      this.loading = true;
      this.error = null;
      
      try {
        // 模拟API调用
        const data = await this.fetchData();
        this.items = data;
      } catch (error) {
        this.error = error.message;
        if (this.onError) {
          this.onError(error);
        }
      } finally {
        this.loading = false;
      }
    },
    
    fetchData() {
      // 模拟API调用
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve([
            { id: 1, name: 'Item 1', status: 'active' },
            { id: 2, name: 'Item 2', status: 'inactive' },
            { id: 3, name: 'Item 3', status: 'active' },
          ]);
        }, 1000);
      });
    },
  },
  
  mounted() {
    this.loadData();
  },
};
</script>
```

**Pattern 5:** 事件处理和回调
```javascript
// 事件处理和回调

// React事件处理
import React, { useState, useCallback } from 'react';

const EventComponent = ({
  onSubmit,
  onCancel,
  onValueChange,
  defaultValue = '',
}) => {
  const [value, setValue] = useState(defaultValue);
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  // 使用useCallback优化性能
  const handleChange = useCallback((e) => {
    const newValue = e.target.value;
    setValue(newValue);
    
    if (onValueChange) {
      onValueChange(newValue);
    }
  }, [onValueChange]);
  
  const handleSubmit = useCallback(async (e) => {
    e.preventDefault();
    
    if (isSubmitting || !onSubmit) return;
    
    setIsSubmitting(true);
    
    try {
      await onSubmit(value);
    } catch (error) {
      console.error('Submit error:', error);
    } finally {
      setIsSubmitting(false);
    }
  }, [value, isSubmitting, onSubmit]);
  
  const handleCancel = useCallback(() => {
    if (onCancel) {
      onCancel();
    }
    setValue(defaultValue);
  }, [onCancel, defaultValue]);
  
  return (
    <form onSubmit={handleSubmit} className="event-component">
      <input
        type="text"
        value={value}
        onChange={handleChange}
        placeholder="Enter value"
        className="form-input"
      />
      <div className="form-actions">
        <button 
          type="submit" 
          className="btn btn-primary"
          disabled={isSubmitting}
        >
          {isSubmitting ? 'Submitting...' : 'Submit'}
        </button>
        <button 
          type="button" 
          className="btn btn-secondary"
          onClick={handleCancel}
        >
          Cancel
        </button>
      </div>
    </form>
  );
};

// Vue事件处理
<template>
  <form @submit.prevent="handleSubmit" class="event-component">
    <input
      type="text"
      v-model="value"
      @input="handleInput"
      placeholder="Enter value"
      class="form-input"
    />
    <div class="form-actions">
      <button 
        type="submit" 
        class="btn btn-primary"
        :disabled="isSubmitting"
      >
        {{ isSubmitting ? 'Submitting...' : 'Submit' }}
      </button>
      <button 
        type="button" 
        class="btn btn-secondary"
        @click="handleCancel"
      >
        Cancel
      </button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'EventComponent',
  
  props: {
    onSubmit: {
      type: Function,
      default: null,
    },
    onCancel: {
      type: Function,
      default: null,
    },
    onValueChange: {
      type: Function,
      default: null,
    },
    defaultValue: {
      type: String,
      default: '',
    },
  },
  
  data() {
    return {
      value: this.defaultValue,
      isSubmitting: false,
    };
  },
  
  watch: {
    // 监听默认值变化
    defaultValue: {
      handler(newValue) {
        this.value = newValue;
      },
      immediate: true,
    },
  },
  
  methods: {
    handleInput(e) {
      const newValue = e.target.value;
      this.value = newValue;
      
      if (this.onValueChange) {
        this.onValueChange(newValue);
      }
    },
    
    async handleSubmit() {
      if (this.isSubmitting || !this.onSubmit) return;
      
      this.isSubmitting = true;
      
      try {
        await this.onSubmit(this.value);
      } catch (error) {
        console.error('Submit error:', error);
      } finally {
        this.isSubmitting = false;
      }
    },
    
    handleCancel() {
      if (this.onCancel) {
        this.onCancel();
      }
      this.value = this.defaultValue;
    },
  },
};
</script>
```

## Examples

### Example 1: 完整的React组件

**Scenario:** 创建一个完整的React列表组件，包含分页、搜索和排序功能。

**Steps:**
1. 定义组件Props
2. 实现状态管理
3. 添加事件处理
4. 实现分页、搜索和排序逻辑
5. 测试组件

**Code:**
```jsx
import React, { useState, useEffect, useCallback } from 'react';
import PropTypes from 'prop-types';
import './ListComponent.css';

/**
 * 列表组件
 * @param {Object} props - 组件属性
 * @returns {JSX.Element} - 组件元素
 */
const ListComponent = ({
  // 数据属性
  items = [],
  // 配置属性
  config = {
    pagination: true,
    search: true,
    sort: true,
  },
  // 分页属性
  pageSize = 10,
  // 回调函数
  onItemClick,
  onPageChange,
  onSortChange,
  // 样式属性
  className = '',
  // 国际化
  messages = {
    searchPlaceholder: 'Search...',
    noItems: 'No items found',
    loading: 'Loading...',
  },
}) => {
  // 状态管理
  const [currentPage, setCurrentPage] = useState(1);
  const [searchTerm, setSearchTerm] = useState('');
  const [sortConfig, setSortConfig] = useState({ key: null, direction: 'ascending' });
  const [filteredItems, setFilteredItems] = useState([]);
  
  // 计算分页数据
  const totalPages = Math.ceil(filteredItems.length / pageSize);
  const paginatedItems = filteredItems.slice(
    (currentPage - 1) * pageSize,
    currentPage * pageSize
  );
  
  // 过滤和排序数据
  useEffect(() => {
    let result = [...items];
    
    // 搜索
    if (searchTerm) {
      result = result.filter(item => 
        Object.values(item).some(value => 
          String(value).toLowerCase().includes(searchTerm.toLowerCase())
        )
      );
    }
    
    // 排序
    if (sortConfig.key) {
      result.sort((a, b) => {
        if (a[sortConfig.key] < b[sortConfig.key]) {
          return sortConfig.direction === 'ascending' ? -1 : 1;
        }
        if (a[sortConfig.key] > b[sortConfig.key]) {
          return sortConfig.direction === 'ascending' ? 1 : -1;
        }
        return 0;
      });
    }
    
    setFilteredItems(result);
    setCurrentPage(1); // 重置到第一页
  }, [items, searchTerm, sortConfig]);
  
  // 处理搜索
  const handleSearch = useCallback((e) => {
    setSearchTerm(e.target.value);
  }, []);
  
  // 处理排序
  const handleSort = useCallback((key) => {
    let direction = 'ascending';
    if (sortConfig.key === key && sortConfig.direction === 'ascending') {
      direction = 'descending';
    }
    
    const newSortConfig = { key, direction };
    setSortConfig(newSortConfig);
    
    if (onSortChange) {
      onSortChange(newSortConfig);
    }
  }, [sortConfig, onSortChange]);
  
  // 处理分页
  const handlePageChange = useCallback((page) => {
    setCurrentPage(page);
    
    if (onPageChange) {
      onPageChange(page);
    }
  }, [onPageChange]);
  
  // 处理项目点击
  const handleItemClick = useCallback((item) => {
    if (onItemClick) {
      onItemClick(item);
    }
  }, [onItemClick]);
  
  // 渲染分页
  const renderPagination = () => {
    if (!config.pagination || totalPages <= 1) return null;
    
    const pageNumbers = [];
    for (let i = 1; i <= totalPages; i++) {
      pageNumbers.push(i);
    }
    
    return (
      <div className="list-pagination">
        <button 
          onClick={() => handlePageChange(currentPage - 1)}
          disabled={currentPage === 1}
          className="pagination-btn"
        >
          Previous
        </button>
        
        {pageNumbers.map(number => (
          <button
            key={number}
            onClick={() => handlePageChange(number)}
            className={`pagination-btn ${currentPage === number ? 'active' : ''}`}
          >
            {number}
          </button>
        ))}
        
        <button 
          onClick={() => handlePageChange(currentPage + 1)}
          disabled={currentPage === totalPages}
          className="pagination-btn"
        >
          Next
        </button>
      </div>
    );
  };
  
  // 渲染搜索
  const renderSearch = () => {
    if (!config.search) return null;
    
    return (
      <div className="list-search">
        <input
          type="text"
          placeholder={messages.searchPlaceholder}
          value={searchTerm}
          onChange={handleSearch}
          className="search-input"
        />
      </div>
    );
  };
  
  // 渲染排序
  const renderSort = () => {
    if (!config.sort || !items.length) return null;
    
    return (
      <div className="list-sort">
        <select
          onChange={(e) => handleSort(e.target.value)}
          className="sort-select"
        >
          <option value="">Sort by</option>
          {Object.keys(items[0] || {}).map(key => (
            <option key={key} value={key}>
              {key.charAt(0).toUpperCase() + key.slice(1)}
            </option>
          ))}
        </select>
      </div>
    );
  };
  
  return (
    <div className={`list-component ${className}`}>
      {/* 工具栏 */}
      <div className="list-toolbar">
        {renderSearch()}
        {renderSort()}
      </div>
      
      {/* 列表内容 */}
      <div className="list-content">
        {paginatedItems.length === 0 ? (
          <div className="list-empty">{messages.noItems}</div>
        ) : (
          <ul className="list-items">
            {paginatedItems.map((item, index) => (
              <li
                key={item.id || index}
                className="list-item"
                onClick={() => handleItemClick(item)}
              >
                {item.name || JSON.stringify(item)}
              </li>
            ))}
          </ul>
        )}
      </div>
      
      {/* 分页 */}
      {renderPagination()}
    </div>
  );
};

// 属性类型定义
ListComponent.propTypes = {
  // 数据
  items: PropTypes.array,
  // 配置
  config: PropTypes.shape({
    pagination: PropTypes.bool,
    search: PropTypes.bool,
    sort: PropTypes.bool,
  }),
  // 分页大小
  pageSize: PropTypes.number,
  // 回调
  onItemClick: PropTypes.func,
  onPageChange: PropTypes.func,
  onSortChange: PropTypes.func,
  // 样式
  className: PropTypes.string,
  // 国际化
  messages: PropTypes.shape({
    searchPlaceholder: PropTypes.string,
    noItems: PropTypes.string,
    loading: PropTypes.string,
  }),
};

// 默认属性
ListComponent.defaultProps = {
  items: [],
  config: {
    pagination: true,
    search: true,
    sort: true,
  },
  pageSize: 10,
  className: '',
  messages: {
    searchPlaceholder: 'Search...',
    noItems: 'No items found',
    loading: 'Loading...',
  },
};

export default ListComponent;
```

**Expected Output:**
- 搜索功能
- 排序功能
- 分页功能
- 空状态显示
- 响应式设计

### Example 2: 完整的Vue组件

**Scenario:** 创建一个完整的Vue表单组件，包含验证和提交功能。

**Steps:**
1. 定义组件Props
2. 实现表单状态管理
3. 添加验证逻辑
4. 实现提交功能
5. 测试组件

**Code:**
```vue
<template>
  <form @submit.prevent="handleSubmit" class="form-component" :class="className">
    <!-- 表单头部 -->
    <div v-if="title" class="form-header">
      <h2 class="form-title">{{ title }}</h2>
      <p v-if="description" class="form-description">{{ description }}</p>
    </div>
    
    <!-- 表单内容 -->
    <div class="form-content">
      <!-- 姓名字段 -->
      <div class="form-field">
        <label for="name" class="form-label">{{ labels.name }}</label>
        <input
          type="text"
          id="name"
          v-model="formData.name"
          @blur="validateField('name')"
          :class="{ 'form-input': true, 'error': errors.name }"
          :disabled="disabled"
        />
        <div v-if="errors.name" class="form-error">{{ errors.name }}</div>
      </div>
      
      <!-- 邮箱字段 -->
      <div class="form-field">
        <label for="email" class="form-label">{{ labels.email }}</label>
        <input
          type="email"
          id="email"
          v-model="formData.email"
          @blur="validateField('email')"
          :class="{ 'form-input': true, 'error': errors.email }"
          :disabled="disabled"
        />
        <div v-if="errors.email" class="form-error">{{ errors.email }}</div>
      </div>
      
      <!-- 消息字段 -->
      <div class="form-field">
        <label for="message" class="form-label">{{ labels.message }}</label>
        <textarea
          id="message"
          v-model="formData.message"
          @blur="validateField('message')"
          :class="{ 'form-textarea': true, 'error': errors.message }"
          :disabled="disabled"
          rows="4"
        ></textarea>
        <div v-if="errors.message" class="form-error">{{ errors.message }}</div>
      </div>
    </div>
    
    <!-- 表单底部 -->
    <div class="form-footer">
      <button
        type="submit"
        class="form-button primary"
        :disabled="isSubmitting || disabled"
      >
        {{ isSubmitting ? messages.submitting : buttons.submit }}
      </button>
      <button
        type="button"
        class="form-button secondary"
        @click="handleReset"
        :disabled="isSubmitting || disabled"
      >
        {{ buttons.reset }}
      </button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'FormComponent',
  
  props: {
    // 表单标题
    title: {
      type: String,
      default: '',
    },
    // 表单描述
    description: {
      type: String,
      default: '',
    },
    // 初始数据
    initialData: {
      type: Object,
      default: () => ({
        name: '',
        email: '',
        message: '',
      }),
    },
    // 是否禁用
    disabled: {
      type: Boolean,
      default: false,
    },
    // 回调函数
    onSubmit: {
      type: Function,
      default: null,
    },
    onReset: {
      type: Function,
      default: null,
    },
    onValueChange: {
      type: Function,
      default: null,
    },
    // 样式
    className: {
      type: String,
      default: '',
    },
    // 国际化
    labels: {
      type: Object,
      default: () => ({
        name: 'Name',
        email: 'Email',
        message: 'Message',
      }),
    },
    buttons: {
      type: Object,
      default: () => ({
        submit: 'Submit',
        reset: 'Reset',
      }),
    },
    messages: {
      type: Object,
      default: () => ({
        submitting: 'Submitting...',
        success: 'Form submitted successfully!',
        error: 'Please fix the errors in the form.',
      }),
    },
  },
  
  data() {
    return {
      // 表单数据
      formData: {
        name: '',
        email: '',
        message: '',
      },
      // 错误信息
      errors: {},
      // 提交状态
      isSubmitting: false,
    };
  },
  
  watch: {
    // 监听初始数据变化
    initialData: {
      handler(newData) {
        this.formData = { ...newData };
      },
      deep: true,
      immediate: true,
    },
    
    // 监听表单数据变化
    formData: {
      handler(newData) {
        if (this.onValueChange) {
          this.onValueChange(newData);
        }
      },
      deep: true,
    },
  },
  
  methods: {
    // 验证字段
    validateField(field) {
      this.errors[field] = null;
      
      switch (field) {
        case 'name':
          if (!this.formData.name.trim()) {
            this.errors.name = 'Name is required';
          }
          break;
        case 'email':
          if (!this.formData.email.trim()) {
            this.errors.email = 'Email is required';
          } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(this.formData.email)) {
            this.errors.email = 'Invalid email format';
          }
          break;
        case 'message':
          if (!this.formData.message.trim()) {
            this.errors.message = 'Message is required';
          } else if (this.formData.message.length < 10) {
            this.errors.message = 'Message must be at least 10 characters';
          }
          break;
        default:
          break;
      }
    },
    
    // 验证所有字段
    validateAll() {
      Object.keys(this.formData).forEach(field => {
        this.validateField(field);
      });
      
      return Object.keys(this.errors).length === 0;
    },
    
    // 处理提交
    async handleSubmit() {
      if (!this.validateAll()) {
        return;
      }
      
      if (!this.onSubmit) {
        return;
      }
      
      this.isSubmitting = true;
      
      try {
        await this.onSubmit(this.formData);
      } catch (error) {
        console.error('Submit error:', error);
      } finally {
        this.isSubmitting = false;
      }
    },
    
    // 处理重置
    handleReset() {
      this.formData = { ...this.initialData };
      this.errors = {};
      
      if (this.onReset) {
        this.onReset();
      }
    },
  },
};
</script>

<style scoped>
.form-component {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 20px;
  background-color: #fff;
}

.form-header {
  margin-bottom: 20px;
}

.form-title {
  margin: 0 0 8px 0;
  font-size: 18px;
  font-weight: 600;
}

.form-description {
  margin: 0;
  color: #666;
  font-size: 14px;
}

.form-content {
  margin-bottom: 20px;
}

.form-field {
  margin-bottom: 16px;
}

.form-label {
  display: block;
  margin-bottom: 6px;
  font-weight: 500;
}

.form-input,
.form-textarea {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  transition: border-color 0.2s ease;
}

.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: #2196f3;
  box-shadow: 0 0 0 2px rgba(33, 150, 243, 0.1);
}

.form-input.error,
.form-textarea.error {
  border-color: #f44336;
}

.form-error {
  margin-top: 4px;
  color: #f44336;
  font-size: 12px;
}

.form-footer {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.form-button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.form-button.primary {
  background-color: #2196f3;
  color: white;
}

.form-button.primary:hover:not(:disabled) {
  background-color: #1976d2;
}

.form-button.secondary {
  background-color: #f5f5f5;
  color: #333;
  border: 1px solid #ddd;
}

.form-button.secondary:hover:not(:disabled) {
  background-color: #e0e0e0;
}

.form-button:disabled {
  background-color: #bdbdbd;
  color: #757575;
  cursor: not-allowed;
}

.form-empty {
  padding: 40px 20px;
  text-align: center;
  color: #666;
}
</style>
```

**Expected Output:**
- 表单验证
- 错误提示
- 提交状态管理
- 重置功能
- 响应式设计

## References

- `references/index.md`: 组件脚手架最佳实践导航
- `references/react.md`: React组件开发指南
- `references/vue.md`: Vue组件开发指南
- `references/props.md`: Props设计指南
- `references/state.md`: 状态管理指南
- `references/styles.md`: 组件样式指南

## Maintenance

- Sources: React Documentation, Vue Documentation, CSS Modules, styled-components
- Last updated: 2026-01-21
- Known limits: 不处理复杂的业务逻辑和性能优化