---
name: laravel-boost
description: Laravel Boost 集成指南，MCP Server 配置和 AI 辅助开发最佳实践
---

# Laravel Boost 集成

Laravel Boost 加速 AI 辅助开发，提供指南、技能和 MCP 工具支持。

## 激活时机

- 配置 Laravel Boost
- 使用 MCP Server 工具
- 创建自定义 AI 技能
- 集成文档 API

## 安装配置

### 安装 Boost

```bash
composer require laravel/boost --dev
php artisan boost:install
```

### MCP Server 配置

在 `.mcp.json` 中配置：

```json
{
  "mcpServers": {
    "laravel-boost": {
      "command": "php",
      "args": ["artisan", "boost:mcp"]
    }
  }
}
```

## MCP 工具

### Search Docs
查询 Laravel 文档 API，获取 17,000+ 条 Laravel 知识：

```
使用 Search Docs 工具查询:
- Laravel 框架文档
- 已安装包的文档
- 最佳实践指南
```

### 数据库查询
通过 MCP 查询数据库结构：

```
查看表结构、索引、关联关系
```

### 代码执行
在应用上下文中执行代码片段验证逻辑

## AI Guidelines

### 默认指南位置
- `CLAUDE.md` - Claude Code 指南
- `AGENTS.md` - 通用代理指南
- `.cursor/rules/` - Cursor 规则

### 添加自定义指南

在 `boost.json` 中配置：

```json
{
  "guidelines": {
    "custom": [
      ".ai/guidelines/project-conventions.md"
    ]
  }
}
```

## 自定义技能

### 创建技能

在 `.ai/skills/{skill-name}/SKILL.md` 创建：

```markdown
---
name: my-custom-skill
description: 自定义业务逻辑技能
---

# 技能内容
```

### 更新技能

```bash
php artisan boost:update
```

## 第三方包技能

安装包后，对应技能自动加载：

| 包 | 自动加载技能 |
|---|---|
| livewire/livewire | livewire-development |
| inertiajs/inertia-laravel | inertia-development |
| filament/filament | filament-development |

## Guidelines vs Skills

| 类型 | 加载时机 | 用途 |
|------|---------|------|
| Guidelines | 启动时加载 | 通用规范和约定 |
| Skills | 按需加载 | 特定领域详细模式 |

## 快速参考

```bash
# 安装
composer require laravel/boost --dev

# 初始化
php artisan boost:install

# 更新
php artisan boost:update

# MCP Server
php artisan boost:mcp
```
