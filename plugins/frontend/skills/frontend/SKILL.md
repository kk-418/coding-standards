---
name: frontend
description: 前端开发规范,包含React/Vue + TypeScript项目标准、pnpm包管理、组件开发、样式规范等。当开发前端项目、创建React/Vue组件、配置TypeScript、编写样式时使用此Skill。
---

# 前端开发规范

本 Skill 提供 React/Vue + TypeScript 前端项目的完整开发规范。

## 核心规范 (frontend-standards.md)

### 强制性规范

- **包管理器**:
  - ✅ 强制使用 `pnpm`
  - ❌ 禁止使用 `npm` 或 `yarn`
- **类型安全**:
  - 必须使用 TypeScript
  - 避免使用 `any` 类型
- **组件规范**:
  - 使用函数组件 + Hooks
  - 避免使用类组件
- **样式隔离**:
  - 推荐使用 CSS Modules
  - 避免全局样式污染

### 技术栈要求

**React 项目:**
- React 18+
- TypeScript 5+
- Vite 或 Next.js
- TanStack Query (React Query)
- Zustand / Redux Toolkit

**Vue 项目:**
- Vue 3+
- TypeScript 5+
- Vite
- Pinia
- VueUse

### 核心内容

1. **包管理器规范** - pnpm 使用和配置
2. **项目结构规范** - 目录组织和命名
3. **代码风格规范** - ESLint + Prettier
4. **TypeScript 规范** - 类型定义和使用
5. **React 开发规范** - 组件、Hooks、状态管理
6. **Vue 开发规范** - 组合式 API、响应式
7. **样式规范** - CSS Modules、Tailwind CSS
8. **API 请求规范** - TanStack Query 使用
9. **性能优化** - 代码分割、懒加载
10. **测试规范** - Vitest、Testing Library

## 使用场景

- 初始化前端项目
- 配置 TypeScript 和构建工具
- 创建 React/Vue 组件
- 实现状态管理
- 封装 API 请求
- 编写样式和实现响应式布局
- 性能优化
- 编写单元测试和组件测试

## 详细文档

请参考同目录下的:
- `frontend-standards.md` - 前端开发完整规范

---

**作者**: kk
**版本**: 1.0.0
**最后更新**: 2025-10-22
