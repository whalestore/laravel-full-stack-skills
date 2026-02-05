---
description: 生成项目蓝图，监控整体进度和结构
---

# 项目蓝图工作流

生成并维护项目蓝图文档，跟踪开发进度、功能状态和质量指标。

## 步骤 1: 扫描项目结构
// turbo
分析项目目录结构：
- `app/` - 控制器、模型、服务
- `routes/` - 路由定义
- `resources/js/Pages/` - Inertia 页面
- `tests/` - 测试文件
- `database/migrations/` - 数据库迁移

## 步骤 2: 识别功能模块

根据路由和控制器识别主要功能模块：
```bash
php artisan route:list --json
```

按以下维度分类：
- 用户认证
- 核心业务
- 管理后台
- API 端点

## 步骤 3: 分析测试覆盖率
// turbo
运行测试并获取覆盖率：
```bash
php artisan test --coverage --coverage-clover=coverage.xml
```

提取覆盖率数据：
- 总体覆盖率
- 各模块覆盖率
- 未覆盖的关键路径

## 步骤 4: 评估代码质量

运行静态分析：
```bash
./vendor/bin/phpstan analyse --level=max --error-format=json
```

检查点：
- 复杂度评分
- 代码规范
- 潜在问题

## 步骤 5: 生成蓝图文档

在项目根目录创建 `blueprint.md`：

```markdown
# 项目蓝图: [项目名称]

## 项目概览
| 指标 | 值 |
|------|-----|
| 技术栈 | Laravel 12.x + React + Inertia.js |
| 测试覆盖率 | XX% |
| 代码质量 | A/B/C |

## 功能清单

### [功能名称]
| 状态 | 进度 | 测试 | 审查 |
|------|------|------|------|
| 🟢/🟡/🔴 | XX% | XX% | ✅/⏳/❌ |

#### 实现目标
- [x] 已完成项
- [ ] 待完成项

## 技术债务
- 待处理项目列表

## 下一步计划
- Sprint/迭代目标
```

## 步骤 6: 更新追踪

后续运行时：
1. 比较上次蓝图
2. 标注进度变化
3. 更新质量指标

## 输出

- `blueprint.md` - 项目蓝图文档
- 控制台输出 - 关键变化摘要
