# 前端开发规范

快速加载前端开发规范,包含 React/Vue + TypeScript 项目标准。

请参考以下规范文档,并在生成代码时严格遵守:

## 核心规范

### 1. 包管理器规范(强制)
- ✅ 强制使用 `pnpm`
- ❌ 禁止使用 `npm` 或 `yarn`

### 2. 类型安全规范(强制)
- 必须使用 TypeScript
- 避免使用 `any` 类型
- 为组件 props 定义接口

### 3. 组件开发规范(强制)
- 使用函数组件 + Hooks
- 避免使用类组件
- 组件文件名使用 PascalCase

### 4. 样式规范(推荐)
- 推荐使用 CSS Modules 样式隔离
- 或使用 Tailwind CSS utility-first
- 避免全局样式污染

### 5. 状态管理规范
- React: 使用 Zustand 或 Redux Toolkit
- Vue: 使用 Pinia
- 避免过度使用全局状态

### 6. API 请求规范
- 使用 TanStack Query (React Query)
- 统一错误处理
- 请求和响应类型定义

## 技术栈

**React 项目:**
- React 18+
- TypeScript 5+
- Vite 或 Next.js
- TanStack Query
- Zustand

**Vue 项目:**
- Vue 3+
- TypeScript 5+
- Vite
- Pinia
- VueUse

## 详细文档

完整规范请参考 `skills/frontend/frontend-standards.md`
