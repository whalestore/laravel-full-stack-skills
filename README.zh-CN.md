# Laravel 全栈技能包

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Laravel](https://img.shields.io/badge/-Laravel-FF2D20?logo=laravel&logoColor=white)
![React](https://img.shields.io/badge/-React-61DAFB?logo=react&logoColor=black)
![Inertia.js](https://img.shields.io/badge/-Inertia.js-9553E9?logo=inertia&logoColor=white)

**语言:** [English](README.md) | 中文

为 **Laravel + React (Inertia.js)** 开发设计的综合技能包，包含 **测试驱动开发 (TDD)** 工作流和 **多模型代码审查** 自动化。

## ✨ 特性

- 🎯 **Laravel 专注技能** - 架构模式、API 设计、Eloquent、Boost 集成
- 🔄 **TDD 工作流** - 严格的 RED-GREEN-REFACTOR 循环，80%+ 覆盖率
- 🤖 **多模型审查** - Gemini/Codex/智谱 并行代码审查
- 🔧 **自动修复循环** - 自动问题修复，带重试限制
- 📊 **项目蓝图** - 跟踪进度和质量指标

---

## 📁 项目结构

```
laravel-full-stack-skills/
├── skills/                  # AI 技能 (SKILL.md 文件)
│   ├── laravel-boost/       # Laravel Boost MCP 集成
│   ├── laravel-patterns/    # 架构模式
│   ├── laravel-tdd/         # TDD 最佳实践
│   ├── laravel-api/         # API Resource, Sanctum
│   ├── inertia-react/       # Inertia.js + React 模式
│   ├── multi-model-review/  # 多模型审查配置
│   ├── auto-fix-loop/       # 自动修复循环控制
│   ├── frontend-patterns/   # 前端最佳实践
│   ├── postgres-patterns/   # PostgreSQL 模式
│   └── security-review/     # 安全审查清单
├── workflows/               # 开发工作流
│   ├── blueprint.md         # 项目监控
│   ├── laravel-tdd-dev.md   # TDD 开发
│   ├── code-review.md       # 多模型审查
│   └── dev-cycle.md         # TDD + 审查组合
├── commands/                # 斜杠命令
│   ├── blueprint.md         # /blueprint
│   ├── tdd-dev.md           # /tdd-dev
│   ├── code-review-loop.md  # /code-review
│   └── dev-cycle.md         # /dev-cycle
├── agents/                  # Agent 配置
│   ├── tdd-guide.md         # TDD 专家
│   ├── code-reviewer.md     # 代码审查员
│   ├── security-reviewer.md # 安全专家
│   └── database-reviewer.md # 数据库专家
└── rules/                   # 代码规范
```

---

## 🚀 快速开始

### 安装

1. 克隆此仓库到你的项目：

```bash
git clone https://github.com/whalestore/laravel-full-stack-skills.git .ai/skills
```

2. 或作为 Git 子模块添加：

```bash
git submodule add https://github.com/whalestore/laravel-full-stack-skills.git .ai/skills
```

### 在 Antigravity 中使用

1. 打开 Antigravity 编辑器
2. 技能会从 `.ai/skills/` 目录自动加载
3. 使用斜杠命令触发工作流

---

## 📋 工作流详解

### 1. 项目蓝图 (`/blueprint`)

创建、扫描或修改项目蓝图文档。

**三种模式：**

| 模式 | 说明 |
|------|------|
| `--new <描述>` | 从提示词创建蓝图 |
| `--scan` | 扫描现有项目（默认） |
| `--edit` | 交互式修改 |

**示例：**
```bash
# 从提示词创建
/blueprint --new "电商平台，支持多店铺、订单管理"

# 扫描现有项目
/blueprint

# 交互式编辑
/blueprint --edit
```

**交互式命令 (--edit 模式)：**
- `添加功能: 支付集成` - 添加新功能
- `更新进度: 用户认证 -> 100%` - 更新进度
- `添加待办: 实现邮件通知` - 添加待办事项

---

### 2. TDD 开发 (`/tdd-dev`)

严格遵循 RED-GREEN-REFACTOR 循环的测试驱动开发工作流。

```
/tdd-dev <功能描述>
```

**工作流步骤：**

| 步骤 | 阶段 | 描述 |
|------|------|------|
| 1 | 分析 | 解析需求 |
| 2 | RED | 编写失败测试 |
| 3 | GREEN | 实现最小代码 |
| 4 | REFACTOR | 优化代码 |
| 5 | 覆盖率 | 确保 80%+ 覆盖率 |
| 6 | 浏览器测试 | 可选：Antigravity 浏览器测试 |
| 7 | 移动端测试 | 可选：Maestro MCP 测试 |
| 8 | 提交 | 自动提交代码 |

**示例：**
```bash
/tdd-dev 实现带筛选条件的产品搜索
```

**TDD 循环：**
```
┌─────────┐     ┌─────────┐     ┌──────────┐
│   RED   │ ──→ │  GREEN  │ ──→ │ REFACTOR │
│ 写测试  │     │ 最小实现 │     │ 优化代码 │
│ (失败)  │     │         │     │         │
└─────────┘     └─────────┘     └──────────┘
      ↑                               │
      └───────────────────────────────┘
              下一个测试用例
```

---

### 3. 代码审查 (`/code-review`)

多模型并行代码审查，带自动修复循环。

```
/code-review [--range=<ref>] [--max-retries=<n>]
```

**审查流程：**

```
                 ┌──────────────────────┐
                 │      收集变更         │
                 └──────────┬───────────┘
                            │
         ┌──────────────────┼──────────────────┐
         ▼                  ▼                  ▼
   ┌───────────┐      ┌───────────┐      ┌───────────┐
   │  Gemini   │      │   Codex   │      │   智谱    │
   │  架构设计  │      │  安全性   │      │  代码风格 │
   └─────┬─────┘      └─────┬─────┘      └─────┬─────┘
         │                  │                  │
         └──────────────────┼──────────────────┘
                            ▼
                 ┌──────────────────────┐
                 │      汇总问题         │
                 │  优先级: C/H/M/L      │
                 └──────────┬───────────┘
                            │
            ┌───────────────┼───────────────┐
            ▼               ▼               ▼
         无问题          可修复          严重
            │               │               │
            ▼               ▼               ▼
         通过 ✅       自动修复 🔄    人工干预 🛑
                            │
                     (最多 3 次重试)
```

**优先级说明：**

| 级别 | 描述 | 处理方式 |
|------|------|----------|
| 🔴 Critical | 安全漏洞 | 人工干预 |
| 🟠 High | 性能、逻辑错误 | 自动修复 |
| 🟡 Medium | 代码质量 | 自动修复 |
| 🟢 Low | 建议优化 | 可选 |

---

### 4. 完整开发循环 (`/dev-cycle`)

组合 TDD + 代码审查的完整工作流。

```
/dev-cycle <功能描述>
```

**完整流程：**

```
┌─────────────────────────────────────────────────┐
│            阶段 1: TDD 开发                      │
│         RED → GREEN → REFACTOR → 测试            │
└────────────────────────┬────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────┐
│            阶段 2: 代码审查                      │
│       多模型审查 → 自动修复循环                   │
└────────────────────────┬────────────────────────┘
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       通过 ✅      需修复 🔄     需干预 🛑
          │              │              │
          │         返回阶段 1      停止并报告
          │         (修复问题)
          ▼
┌─────────────────────────────────────────────────┐
│            阶段 3: 更新蓝图                      │
│           记录进度和指标                         │
└─────────────────────────────────────────────────┘
```

---

## 🛠 技能参考

### Laravel 技能

| 技能 | 描述 |
|------|------|
| `laravel-boost` | MCP Server 集成、AI 指南 |
| `laravel-patterns` | 控制器、服务层、Repository |
| `laravel-tdd` | PHPUnit/Pest、TDD 工作流 |
| `laravel-api` | API Resource、Sanctum 认证 |
| `inertia-react` | 页面组件、表单、路由 |

### 审查技能

| 技能 | 描述 |
|------|------|
| `multi-model-review` | 配置多个审查模型 |
| `auto-fix-loop` | 自动修复循环控制 |
| `security-review` | 安全检查清单 |

---

## 🔧 在 Antigravity 中使用

### 设置

1. **克隆技能包到项目：**
   ```bash
   cd your-laravel-project
   git clone https://github.com/whalestore/laravel-full-stack-skills.git .ai/skills
   ```

2. **使用 Antigravity 打开：**
   - 启动 Antigravity 编辑器
   - 打开你的 Laravel 项目
   - 技能会自动检测加载

### 命令使用

在 Antigravity 聊天中输入以下命令：

| 命令 | 描述 |
|------|------|
| `/blueprint` | 生成项目蓝图 |
| `/tdd-dev <功能>` | 启动 TDD 开发 |
| `/code-review` | 运行代码审查 |
| `/dev-cycle <功能>` | 完整开发循环 |

### 浏览器测试

TDD 工作流支持 Antigravity 内置的浏览器工具：

1. 开发 Web 页面时，浏览器测试自动运行
2. 截图和录像会保存用于验证
3. UI 流程会根据预期行为进行验证

### MCP 集成

在项目中配置 Laravel Boost MCP：

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

---

## 📄 许可证

MIT 许可证 - 详见 [LICENSE](LICENSE) 文件。

---

## 🙏 致谢

基于 [everything-claude-code](https://github.com/affaan-m/everything-claude-code)，为 Laravel 全栈开发进行了适配。
