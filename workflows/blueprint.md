---
description: 创建、扫描、修改项目蓝图，或生成 OpenSpec 任务列表
---

# 项目蓝图工作流

支持四种模式：
- **模式 A**: 从提示词创建新项目蓝图 (`--new`)
- **模式 B**: 扫描现有项目生成蓝图 (`--scan`)
- **模式 C**: 交互式修改蓝图 (`--edit`)
- **模式 D**: 生成 OpenSpec 任务列表 (`--openspec`)

---

## 前置检查: OpenSpec 初始化

如果用户需要 OpenSpec 功能，先检查是否已初始化：

```bash
# 检查 openspec 目录是否存在
if [ ! -d "openspec" ]; then
    echo "请先运行: npm install -g @fission-ai/openspec@latest && openspec init"
fi
```

---

## 模式 A: 从提示词创建 (`--new`)

### 步骤 1: 解析需求描述

从用户提示词中提取：
- 项目名称和描述
- 核心功能模块
- 技术栈要求
- 目标用户

### 步骤 2: 生成功能分解

将需求分解为可开发的功能模块（使用 OpenSpec 兼容格式）：

```markdown
## 功能分解

### 1. 用户认证模块
- [ ] 1.1 用户注册
- [ ] 1.2 用户登录
- [ ] 1.3 密码重置
- [ ] 1.4 邮箱验证

### 2. 核心业务模块
- [ ] 2.1 功能 A
- [ ] 2.2 功能 B
...
```

### 步骤 3: UI 设计规范 (Shadcn UI)

为每个模块定义 UI 组件：

```markdown
## UI 组件规划

### 布局组件
- Sidebar (侧边栏导航)
- Header (顶部导航)
- Card (内容卡片)

### 表单组件
- Input, Label, Button
- Select, Checkbox, Switch
- Form validation states

### 数据展示
- Table (数据表格)
- Badge (状态标签)
- Pagination (分页)

### 反馈组件
- Toast (消息提示)
- Dialog (对话框)
- AlertDialog (确认框)
```

> 参考技能: `skills/shadcn-ui/SKILL.md`

### 步骤 4: 规划开发阶段

生成迭代计划：

```markdown
## 开发阶段

### Phase 1: 基础框架 (预计 1 周)
- 项目初始化
- Shadcn UI 配置
- 用户认证
- 基础布局

### Phase 2: 核心功能 (预计 2 周)
- 功能 A
- 功能 B

### Phase 3: 完善 (预计 1 周)
- 测试覆盖
- 性能优化
```

### 步骤 5: 生成蓝图文档

创建 `blueprint.md`。

---

## 模式 B: 扫描现有项目 (`--scan` 或默认)

### 步骤 1: 扫描项目结构
// turbo
分析目录结构：
- `app/` - 控制器、模型、服务
- `routes/` - 路由定义
- `resources/js/Pages/` - Inertia 页面
- `tests/` - 测试文件
- `database/migrations/` - 数据库迁移

### 步骤 2: 识别功能模块

从路由和控制器识别模块：
```bash
php artisan route:list --json
```

分类维度：
- 用户认证
- 核心业务
- 管理后台
- API 端点

### 步骤 3: 分析测试覆盖率
// turbo
```bash
php artisan test --coverage --coverage-clover=coverage.xml
```

### 步骤 4: 评估代码质量

```bash
./vendor/bin/phpstan analyse --level=max --error-format=json
```

### 步骤 5: 生成蓝图文档

创建或更新 `blueprint.md`。

---

## 模式 C: 交互式修改 (`--edit`)

### 步骤 1: 读取现有蓝图

加载 `blueprint.md` 内容。

### 步骤 2: 显示当前状态

向用户展示：
- 功能模块列表
- 各模块进度
- 测试覆盖率
- 待办事项

### 步骤 3: 接收修改指令

支持的修改操作：
- **添加功能**: `添加功能: 支付集成`
- **更新进度**: `更新进度: 用户认证 -> 100%`
- **添加待办**: `添加待办: 实现邮件通知`
- **修改描述**: `修改描述: 项目目标是...`
- **删除功能**: `删除功能: 废弃的功能A`

### 步骤 4: 应用修改

更新 `blueprint.md` 文件。

### 步骤 5: 确认变更

显示变更摘要：
```
✅ 已添加功能: 支付集成
✅ 已更新进度: 用户认证 100%
```

---

## 模式 D: 生成 OpenSpec 任务列表 (`--openspec`)

将蓝图转换为 OpenSpec 兼容的 change 文件夹和任务列表。

### 步骤 1: 读取蓝图

加载 `blueprint.md` 或从扫描结果获取功能模块。

### 步骤 2: 为每个功能模块创建 Change

为每个主要功能模块创建独立的 change 文件夹：

```bash
# 示例：为用户认证模块创建 change
mkdir -p openspec/changes/user-authentication
```

生成的目录结构：
```
openspec/changes/
├── user-authentication/
│   ├── proposal.md
│   ├── design.md
│   ├── tasks.md
│   └── specs/
├── core-business/
│   ├── proposal.md
│   ├── design.md
│   ├── tasks.md
│   └── specs/
└── ...
```

### 步骤 3: 生成 Proposal

为每个 change 生成 `proposal.md`：

```markdown
# Proposal: [模块名称]

## Intent
[模块目的和用户价值]

## Scope
In scope:
- [具体功能点]

Out of scope:
- [不包含的功能]

## Approach
[实现方案概述]
```

### 步骤 4: 生成 Tasks

为每个 change 生成 `tasks.md`，使用层级编号：

```markdown
# Tasks

## 1. [功能组名称]
- [ ] 1.1 [子任务描述]
- [ ] 1.2 [子任务描述]
- [ ] 1.3 [子任务描述]

## 2. [功能组名称]
- [ ] 2.1 [子任务描述]
- [ ] 2.2 [子任务描述]
```

### 步骤 5: 生成 Design (可选)

如果功能复杂，生成 `design.md`：

```markdown
# Design: [模块名称]

## Technical Approach
[技术实现方案]

## Architecture Decisions

### Decision: [决策点]
[选择原因和权衡]

## Data Flow
[数据流图或说明]

## File Changes
- `path/to/file.php` (new/modified)
```

### 步骤 6: 输出摘要

```
✅ 已创建 3 个 OpenSpec changes:
   - openspec/changes/user-authentication/ (8 tasks)
   - openspec/changes/core-business/ (12 tasks)
   - openspec/changes/admin-panel/ (6 tasks)

下一步: 使用 /opsx:apply <change-name> 开始实施
```

---

## 蓝图模板

```markdown
# 项目蓝图: [项目名称]

> [项目简述]

## 项目概览

| 指标 | 值 |
|------|-----|
| 技术栈 | Laravel 12.x + React + Inertia.js |
| UI 框架 | Shadcn/UI |
| 测试覆盖率 | XX% |
| 代码质量 | A/B/C |
| 创建时间 | YYYY-MM-DD |
| 最后更新 | YYYY-MM-DD |


## 功能清单

### 模块 1: [模块名称]

| 状态 | 进度 | 测试覆盖 | 审查状态 |
|------|------|---------| ---------|
| 🟢/🟡/🔴 | XX% | XX% | ✅/⏳/❌ |

#### 功能点
- [x] 1.1 已完成功能
- [ ] 1.2 待实现功能

#### 技术要点
- 关键实现说明

---

### 模块 2: [模块名称]
...

## 开发计划

### 当前迭代
- [ ] 任务 1
- [ ] 任务 2

### 下一迭代
- [ ] 任务 3
- [ ] 任务 4

## 技术债务

| 问题 | 优先级 | 状态 |
|------|--------|------|
| 问题描述 | 高/中/低 | 待处理/已解决 |

## 备注

- 补充说明
```

---

## OpenSpec 任务模板

### proposal.md 模板

```markdown
# Proposal: [功能名称]

## Intent
[用户需要这个功能的原因和预期价值]

## Scope

In scope:
- [功能点 1]
- [功能点 2]

Out of scope:
- [明确排除的功能]

## Approach
[高层次的技术方案]
```

### tasks.md 模板

```markdown
# Tasks

## 1. [功能组 - 如: 后端实现]
- [ ] 1.1 [具体任务]
- [ ] 1.2 [具体任务]

## 2. [功能组 - 如: 前端实现]
- [ ] 2.1 [具体任务]
- [ ] 2.2 [具体任务]

## 3. [功能组 - 如: 测试与验证]
- [ ] 3.1 编写单元测试
- [ ] 3.2 集成测试
- [ ] 3.3 手动验证
```

### design.md 模板

```markdown
# Design: [功能名称]

## Technical Approach
[技术实现的详细方案]

## Architecture Decisions

### Decision: [决策名称]
选择 [方案 A] 因为：
- [原因 1]
- [原因 2]

## File Changes
- `app/Http/Controllers/XxxController.php` (new)
- `resources/js/Pages/Xxx.tsx` (new)
- `routes/web.php` (modified)
```

---

## 输出

- `blueprint.md` - 项目蓝图文档
- `openspec/changes/*/` - OpenSpec change 文件夹 (使用 `--openspec` 模式)
- 控制台 - 操作摘要

---

## 与 OpenSpec 命令集成

完成 `--openspec` 模式后，可使用以下 OpenSpec 命令：

| 命令 | 说明 |
|------|------|
| `/opsx:apply <change>` | 实施指定 change 的任务 |
| `/opsx:verify <change>` | 验证实施是否符合规格 |
| `/opsx:archive <change>` | 归档已完成的 change |
| `/opsx:bulk-archive` | 批量归档所有已完成的 changes |
