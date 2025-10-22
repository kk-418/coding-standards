---
name: build-tools
description: Gradle和Maven构建工具配置规范。当配置项目构建、管理依赖、设置编译选项、配置注解处理器时使用此Skill。支持Gradle 9.0+和Maven 3.9+。
---

# 构建工具配置规范

本 Skill 提供 Gradle 和 Maven 项目的构建配置标准。

## Gradle 规范 (gradle-standards.md)

**适用版本**: Gradle 9.0+

**核心配置:**
- 项目结构规范
- 编译器配置(Java 21+)
- 依赖管理(版本目录)
- 注解处理器配置(Lombok, MapStruct等)
- 构建优化(并行构建、缓存)
- 发布配置

**关键最佳实践:**
- 使用 Gradle Wrapper 锁定版本
- 启用依赖版本目录(version catalog)
- 配置构建缓存提升性能
- 合理使用任务依赖关系

## Maven 规范 (maven-standards.md)

**适用版本**: Maven 3.9+

**核心配置:**
- POM 文件规范
- 依赖管理(BOM)
- 插件配置
- 多模块项目结构
- Profile 配置
- 发布配置

**关键最佳实践:**
- 使用 Maven Wrapper 锁定版本
- 使用 BOM 统一依赖版本
- 合理配置编译器插件
- 多模块项目的依赖管理

## 使用场景

- 初始化新的 Java 项目
- 配置构建工具和编译选项
- 添加和管理项目依赖
- 配置注解处理器(Lombok等)
- 多模块项目架构设计
- CI/CD 构建流程配置

## 详细文档

请参考同目录下的:
- `gradle-standards.md` - Gradle 完整配置指南
- `maven-standards.md` - Maven 完整配置指南

---

**作者**: kk
**版本**: 1.0.0
**最后更新**: 2025-10-22
