# 前端编码规范

> **作者**: kk
> **创建日期**: 2025-10-01
> **适用范围**: 前端项目开发(React/Vue/TypeScript)

---

## 目录
1. [技术栈要求](#技术栈要求)
2. [包管理器规范](#包管理器规范)
3. [项目结构规范](#项目结构规范)
4. [代码风格规范](#代码风格规范)
5. [TypeScript规范](#typescript规范)
6. [React开发规范](#react开发规范)
7. [Vue开发规范](#vue开发规范)
8. [样式规范](#样式规范)
9. [API请求规范](#api请求规范)
10. [性能优化](#性能优化)
11. [测试规范](#测试规范)
12. [最佳实践](#最佳实践)

---

## 1. 技术栈要求

### 1.1 核心技术栈

**React技术栈**:
- React 18+
- TypeScript 5+
- Vite 5+ (构建工具)
- React Router 6+
- Zustand / Redux Toolkit (状态管理)
- TanStack Query (数据请求)
- Ant Design / Material-UI (UI组件库)

**Vue技术栈**:
- Vue 3+
- TypeScript 5+
- Vite 5+ (构建工具)
- Vue Router 4+
- Pinia (状态管理)
- VueUse (工具库)
- Element Plus / Ant Design Vue (UI组件库)

### 1.2 开发工具要求

- **Node.js**: 20+ LTS版本
- **包管理器**: pnpm 9+ (强制使用)
- **编辑器**: VSCode (推荐)
- **代码格式化**: Prettier
- **代码检查**: ESLint

---

## 2. 包管理器规范

### 2.1 强制使用pnpm

**核心原则**: **禁止使用npm和yarn,统一使用pnpm**

#### 2.1.1 为什么使用pnpm

- **磁盘空间效率**: 使用硬链接,节省磁盘空间
- **安装速度快**: 比npm快2-3倍
- **严格的依赖管理**: 避免幻影依赖(phantom dependencies)
- **Monorepo支持**: 原生支持workspace

#### 2.1.2 安装pnpm

```bash
# 全局安装pnpm
npm install -g pnpm

# 或使用官方安装脚本
curl -fsSL https://get.pnpm.io/install.sh | sh -

# 验证安装
pnpm --version
```

### 2.2 常用pnpm命令

```bash
# 初始化项目
pnpm init

# 安装依赖
pnpm install
pnpm i

# 添加依赖
pnpm add react
pnpm add -D typescript

# 添加全局依赖
pnpm add -g typescript

# 移除依赖
pnpm remove react
pnpm rm react

# 更新依赖
pnpm update
pnpm up

# 运行脚本
pnpm run dev
pnpm dev

# 清理node_modules
pnpm store prune
```

### 2.3 禁用npm/yarn

**项目根目录创建 `.npmrc` 文件**:

```ini
# 禁用npm
engine-strict=true

# pnpm配置
shamefully-hoist=false
strict-peer-dependencies=false
auto-install-peers=true
```

**package.json中限制引擎**:

```json
{
  "engines": {
    "node": ">=20.0.0",
    "pnpm": ">=9.0.0",
    "npm": "请使用pnpm",
    "yarn": "请使用pnpm"
  },
  "packageManager": "pnpm@9.0.0"
}
```

### 2.4 项目脚本配置

**package.json示例**:

```json
{
  "name": "my-frontend",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "format": "prettier --write \"src/**/*.{ts,tsx,css,scss}\"",
    "type-check": "tsc --noEmit",
    "test": "vitest",
    "test:coverage": "vitest --coverage"
  }
}
```

---

## 3. 项目结构规范

### 3.1 React项目结构

```
my-react-app/
├── .vscode/              # VSCode配置
│   └── settings.json
├── public/               # 静态资源
│   └── favicon.ico
├── src/
│   ├── api/              # API请求
│   │   ├── request.ts    # axios封装
│   │   └── payment.ts    # 业务API
│   ├── assets/           # 资源文件
│   │   ├── images/
│   │   └── styles/
│   ├── components/       # 公共组件
│   │   ├── Button/
│   │   │   ├── index.tsx
│   │   │   └── index.module.css
│   │   └── Layout/
│   ├── hooks/            # 自定义Hooks
│   │   └── useAuth.ts
│   ├── pages/            # 页面组件
│   │   ├── Home/
│   │   │   ├── index.tsx
│   │   │   └── index.module.css
│   │   └── Payment/
│   ├── router/           # 路由配置
│   │   └── index.tsx
│   ├── store/            # 状态管理
│   │   └── userStore.ts
│   ├── types/            # TypeScript类型
│   │   └── payment.ts
│   ├── utils/            # 工具函数
│   │   └── format.ts
│   ├── App.tsx
│   ├── main.tsx
│   └── vite-env.d.ts
├── .eslintrc.cjs         # ESLint配置
├── .prettierrc           # Prettier配置
├── .gitignore
├── .npmrc                # pnpm配置
├── index.html
├── package.json
├── pnpm-lock.yaml        # pnpm锁文件
├── tsconfig.json
└── vite.config.ts
```

### 3.2 Vue项目结构

```
my-vue-app/
├── public/
├── src/
│   ├── api/              # API请求
│   ├── assets/           # 资源文件
│   ├── components/       # 公共组件
│   ├── composables/      # 组合式函数
│   │   └── useAuth.ts
│   ├── layouts/          # 布局组件
│   ├── router/           # 路由配置
│   ├── stores/           # Pinia状态管理
│   ├── types/            # TypeScript类型
│   ├── utils/            # 工具函数
│   ├── views/            # 页面组件
│   ├── App.vue
│   └── main.ts
├── .npmrc
├── package.json
├── pnpm-lock.yaml
├── tsconfig.json
└── vite.config.ts
```

---

## 4. 代码风格规范

### 4.1 命名规范

#### 4.1.1 文件命名

```
# React组件文件
Button.tsx              # PascalCase
index.tsx               # 入口文件

# Vue组件文件
UserProfile.vue         # PascalCase
LoginForm.vue

# 工具文件
format.ts               # camelCase
request.ts
userUtils.ts

# 样式文件
index.module.css        # CSS Modules
UserProfile.module.scss
```

#### 4.1.2 变量命名

```typescript
// 常量: UPPER_SNAKE_CASE
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_COUNT = 3;

// 变量/函数: camelCase
const userName = 'John';
const getUserInfo = () => {};

// 组件: PascalCase
const UserProfile = () => {};
const LoginForm = () => {};

// 接口/类型: PascalCase
interface UserInfo {
  id: string;
  name: string;
}

type PaymentStatus = 'pending' | 'success' | 'failed';

// 布尔值: is/has/can开头
const isLoading = false;
const hasPermission = true;
const canEdit = false;
```

### 4.2 ESLint配置

**.eslintrc.cjs**:

```javascript
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react-hooks/recommended',
    'plugin:prettier/recommended'
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    '@typescript-eslint/no-explicit-any': 'warn',
  },
}
```

### 4.3 Prettier配置

**.prettierrc**:

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

---

## 5. TypeScript规范

### 5.1 类型定义

**类型文件 `src/types/payment.ts`**:

```typescript
// 接口定义
export interface PaymentVO {
  id: string;
  amount: string;
  status: PaymentStatus;
  createTime: string;
}

// 类型别名
export type PaymentStatus = 'WAITING_PAYMENT' | 'PAID' | 'CANCELLED' | 'REFUNDED';

// 枚举
export enum OrderType {
  NORMAL = 'NORMAL',
  VIP = 'VIP',
  WHOLESALE = 'WHOLESALE',
}

// 请求参数
export interface CreatePaymentRequest {
  orderId: string;
  amount: string;
  paymentCompany: string;
}

// API响应
export interface ApiResponse<T = any> {
  code: number;
  message: string;
  data: T;
}

// 分页响应
export interface PageResult<T> {
  pageNum: number;
  pageSize: number;
  total: number;
  list: T[];
}
```

### 5.2 类型使用规范

```typescript
// ✅ 推荐: 使用interface定义对象类型
interface User {
  id: string;
  name: string;
}

// ✅ 推荐: 使用type定义联合类型
type Status = 'success' | 'error' | 'loading';

// ❌ 避免: 使用any
const data: any = getData(); // 不推荐

// ✅ 推荐: 使用具体类型或unknown
const data: UserInfo = getData();
const data: unknown = getData();

// ✅ 推荐: 函数类型定义
const fetchUser = async (id: string): Promise<UserInfo> => {
  const response = await api.get<ApiResponse<UserInfo>>(`/users/${id}`);
  return response.data.data;
};

// ✅ 推荐: 使用可选链和空值合并
const userName = user?.profile?.name ?? '未知用户';
```

### 5.3 泛型使用

```typescript
// 泛型函数
function identity<T>(arg: T): T {
  return arg;
}

// 泛型接口
interface Container<T> {
  value: T;
  getValue: () => T;
}

// 泛型约束
interface HasId {
  id: string;
}

function findById<T extends HasId>(items: T[], id: string): T | undefined {
  return items.find((item) => item.id === id);
}
```

---

## 6. React开发规范

### 6.1 组件定义

```typescript
// ✅ 推荐: 函数组件 + TypeScript
import React from 'react';

interface ButtonProps {
  children: React.ReactNode;
  onClick?: () => void;
  type?: 'primary' | 'secondary';
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  children,
  onClick,
  type = 'primary',
  disabled = false
}) => {
  return (
    <button
      className={`btn btn-${type}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

### 6.2 Hooks使用规范

```typescript
import { useState, useEffect, useCallback, useMemo } from 'react';

const UserProfile: React.FC<{ userId: string }> = ({ userId }) => {
  // 1. useState
  const [user, setUser] = useState<UserInfo | null>(null);
  const [loading, setLoading] = useState(false);

  // 2. useEffect
  useEffect(() => {
    const fetchUser = async () => {
      setLoading(true);
      try {
        const data = await getUserById(userId);
        setUser(data);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  // 3. useCallback: 缓存函数
  const handleUpdate = useCallback(() => {
    console.log('Update user:', userId);
  }, [userId]);

  // 4. useMemo: 缓存计算结果
  const displayName = useMemo(() => {
    return user?.name ? `${user.name} (${user.email})` : '加载中...';
  }, [user]);

  if (loading) return <div>加载中...</div>;
  if (!user) return <div>用户不存在</div>;

  return (
    <div>
      <h1>{displayName}</h1>
      <button onClick={handleUpdate}>更新</button>
    </div>
  );
};
```

### 6.3 自定义Hooks

```typescript
// hooks/useAuth.ts
import { useState, useEffect } from 'react';
import { getUserInfo } from '@/api/auth';

export const useAuth = () => {
  const [user, setUser] = useState<UserInfo | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const loadUser = async () => {
      try {
        const data = await getUserInfo();
        setUser(data);
      } catch (error) {
        console.error('Failed to load user:', error);
      } finally {
        setLoading(false);
      }
    };

    loadUser();
  }, []);

  const logout = () => {
    setUser(null);
    localStorage.removeItem('token');
  };

  return { user, loading, logout };
};

// 使用
const MyComponent = () => {
  const { user, loading, logout } = useAuth();

  if (loading) return <div>加载中...</div>;

  return <div>{user?.name}</div>;
};
```

### 6.4 状态管理(Zustand)

```typescript
// store/userStore.ts
import { create } from 'zustand';

interface UserState {
  user: UserInfo | null;
  setUser: (user: UserInfo | null) => void;
  logout: () => void;
}

export const useUserStore = create<UserState>((set) => ({
  user: null,
  setUser: (user) => set({ user }),
  logout: () => {
    set({ user: null });
    localStorage.removeItem('token');
  },
}));

// 使用
const MyComponent = () => {
  const { user, setUser, logout } = useUserStore();

  return (
    <div>
      {user ? (
        <>
          <span>{user.name}</span>
          <button onClick={logout}>退出</button>
        </>
      ) : (
        <button>登录</button>
      )}
    </div>
  );
};
```

---

## 7. Vue开发规范

### 7.1 组件定义

```vue
<!-- components/UserProfile.vue -->
<script setup lang="ts">
import { ref, computed } from 'vue';

// Props定义
interface Props {
  userId: string;
  editable?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  editable: false,
});

// Emits定义
interface Emits {
  (e: 'update', data: UserInfo): void;
  (e: 'delete'): void;
}

const emit = defineEmits<Emits>();

// 响应式数据
const user = ref<UserInfo | null>(null);
const loading = ref(false);

// 计算属性
const displayName = computed(() => {
  return user.value ? `${user.value.name} (${user.value.email})` : '加载中...';
});

// 方法
const handleUpdate = () => {
  if (user.value) {
    emit('update', user.value);
  }
};
</script>

<template>
  <div class="user-profile">
    <h1>{{ displayName }}</h1>
    <button v-if="editable" @click="handleUpdate">更新</button>
  </div>
</template>

<style scoped>
.user-profile {
  padding: 20px;
}
</style>
```

### 7.2 Composables使用

```typescript
// composables/useAuth.ts
import { ref, computed } from 'vue';
import { getUserInfo } from '@/api/auth';

export const useAuth = () => {
  const user = ref<UserInfo | null>(null);
  const loading = ref(true);

  const isLoggedIn = computed(() => !!user.value);

  const loadUser = async () => {
    loading.value = true;
    try {
      const data = await getUserInfo();
      user.value = data;
    } catch (error) {
      console.error('Failed to load user:', error);
    } finally {
      loading.value = false;
    }
  };

  const logout = () => {
    user.value = null;
    localStorage.removeItem('token');
  };

  return {
    user,
    loading,
    isLoggedIn,
    loadUser,
    logout,
  };
};
```

### 7.3 Pinia状态管理

```typescript
// stores/user.ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useUserStore = defineStore('user', () => {
  const user = ref<UserInfo | null>(null);
  const token = ref<string | null>(localStorage.getItem('token'));

  const isLoggedIn = computed(() => !!user.value);

  const setUser = (newUser: UserInfo | null) => {
    user.value = newUser;
  };

  const setToken = (newToken: string | null) => {
    token.value = newToken;
    if (newToken) {
      localStorage.setItem('token', newToken);
    } else {
      localStorage.removeItem('token');
    }
  };

  const logout = () => {
    user.value = null;
    setToken(null);
  };

  return {
    user,
    token,
    isLoggedIn,
    setUser,
    setToken,
    logout,
  };
});
```

---

## 8. 样式规范

### 8.1 CSS Modules(推荐)

```typescript
// Button.tsx
import styles from './Button.module.css';

export const Button: React.FC = ({ children }) => {
  return <button className={styles.button}>{children}</button>;
};
```

```css
/* Button.module.css */
.button {
  padding: 8px 16px;
  border-radius: 4px;
  background-color: #1890ff;
  color: white;
  border: none;
  cursor: pointer;
}

.button:hover {
  background-color: #40a9ff;
}
```

### 8.2 样式命名规范

```css
/* BEM命名规范 */
.user-profile {
}

.user-profile__header {
}

.user-profile__title {
}

.user-profile__title--large {
}

.user-profile--active {
}
```

---

## 9. API请求规范

### 9.1 Axios封装

```typescript
// api/request.ts
import axios, { AxiosError, AxiosRequestConfig } from 'axios';

const request = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
});

// 请求拦截器
request.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// 响应拦截器
request.interceptors.response.use(
  (response) => {
    return response.data;
  },
  (error: AxiosError<ApiResponse>) => {
    const message = error.response?.data?.message || '请求失败';
    console.error('API Error:', message);
    return Promise.reject(error);
  }
);

export default request;
```

### 9.2 API定义

```typescript
// api/payment.ts
import request from './request';
import type { PaymentVO, CreatePaymentRequest, PageResult } from '@/types/payment';

export const paymentApi = {
  // 分页查询
  pageQuery: (pageNum: number, pageSize: number) => {
    return request.get<PageResult<PaymentVO>>('/api/payments', {
      params: { pageNum, pageSize },
    });
  },

  // 根据ID查询
  getById: (id: string) => {
    return request.get<PaymentVO>(`/api/payment/${id}`);
  },

  // 创建支付
  create: (data: CreatePaymentRequest) => {
    return request.post<PaymentVO>('/api/payment', data);
  },

  // 更新状态
  updateStatus: (id: string, status: string) => {
    return request.put(`/api/payment/${id}/status`, { status });
  },
};
```

### 9.3 使用TanStack Query

```typescript
// React
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { paymentApi } from '@/api/payment';

const PaymentList = () => {
  const queryClient = useQueryClient();

  // 查询
  const { data, isLoading, error } = useQuery({
    queryKey: ['payments', 1, 10],
    queryFn: () => paymentApi.pageQuery(1, 10),
  });

  // 变更
  const createMutation = useMutation({
    mutationFn: paymentApi.create,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['payments'] });
    },
  });

  if (isLoading) return <div>加载中...</div>;
  if (error) return <div>加载失败</div>;

  return (
    <div>
      {data?.list.map((payment) => (
        <div key={payment.id}>{payment.amount}</div>
      ))}
    </div>
  );
};
```

---

## 10. 性能优化

### 10.1 代码分割

```typescript
// React Lazy Loading
import { lazy, Suspense } from 'react';

const PaymentPage = lazy(() => import('@/pages/Payment'));

const App = () => {
  return (
    <Suspense fallback={<div>加载中...</div>}>
      <PaymentPage />
    </Suspense>
  );
};
```

### 10.2 图片优化

```typescript
// 使用WebP格式
<img src="/images/banner.webp" alt="Banner" />

// 懒加载
<img src="/images/photo.jpg" loading="lazy" alt="Photo" />
```

### 10.3 虚拟滚动

```typescript
// 使用react-window
import { FixedSizeList } from 'react-window';

const LargeList = ({ items }: { items: any[] }) => {
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {({ index, style }) => <div style={style}>{items[index].name}</div>}
    </FixedSizeList>
  );
};
```

---

## 11. 测试规范

### 11.1 单元测试

```typescript
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('should render correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('should call onClick when clicked', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click me</Button>);

    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

---

## 12. 最佳实践

### 12.1 代码组织

```typescript
// ✅ 推荐: 单一职责
const UserProfile = () => {
  return <UserInfo />;
};

const UserInfo = () => {
  return <div>User Info</div>;
};

// ❌ 避免: 过大的组件
const UserProfile = () => {
  return (
    <div>
      {/* 数百行代码 */}
    </div>
  );
};
```

### 12.2 避免props drilling

```typescript
// ✅ 推荐: 使用Context或状态管理
const UserContext = createContext<UserInfo | null>(null);

const App = () => {
  const user = useAuth();
  return (
    <UserContext.Provider value={user}>
      <ChildComponent />
    </UserContext.Provider>
  );
};
```

### 12.3 错误边界

```typescript
// ErrorBoundary.tsx
import { Component, ErrorInfo, ReactNode } from 'react';

interface Props {
  children: ReactNode;
}

interface State {
  hasError: boolean;
}

export class ErrorBoundary extends Component<Props, State> {
  state: State = { hasError: false };

  static getDerivedStateFromError(): State {
    return { hasError: true };
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error('Error:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>出错了</h1>;
    }

    return this.props.children;
  }
}
```

---

## 参考资料

- [pnpm官方文档](https://pnpm.io/zh/)
- [React官方文档](https://react.dev/)
- [Vue官方文档](https://cn.vuejs.org/)
- [TypeScript官方文档](https://www.typescriptlang.org/)
- [Vite官方文档](https://cn.vitejs.dev/)

---

**最后更新**: 2025-10-01
**维护者**: kk
