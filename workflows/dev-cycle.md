---
description: 完整开发循环 = TDD 开发 + 代码审查
---

# 开发循环工作流

将 TDD 开发和代码审查组合为完整的开发循环。

## 配置

```yaml
max_review_cycles: 3   # 审查循环最大次数
auto_commit: true      # 通过后自动提交
update_blueprint: true # 完成后更新蓝图
```

---

## 阶段 1: TDD 开发

调用 `/tdd-dev` 工作流：

1. 分析需求
2. 编写测试 (RED)
3. 实现代码 (GREEN)
4. **实现 UI (Shadcn)**
   - 使用 `skills/shadcn-ui` 组件
   - 遵循既定布局模式
5. 重构 (REFACTOR)
6. 覆盖率检查
7. 提交代码

> 参考: `workflows/laravel-tdd-dev.md`, `skills/shadcn-ui/SKILL.md`


---

## 阶段 2: 代码审查

调用 `/code-review` 工作流：

```
审查开始
    │
    ├── 通过 ──────────────→ 阶段 3
    │
    └── 有问题
            │
            ├── 可自动修复 ──→ 返回阶段 1
            │                 (仅修复部分)
            │
            └── 需人工干预 ──→ 暂停
                              请求确认
```

### 循环控制

```
cycle_count = 0
while True:
    result = run_code_review()
    
    if result == "passed":
        break
    elif result == "needs_fix":
        cycle_count += 1
        if cycle_count >= max_review_cycles:
            request_human_intervention()
            break
        run_tdd_fix(result.issues)
    elif result == "needs_human":
        request_human_intervention()
        break
```

> 参考: `workflows/code-review.md`

---

## 阶段 3: 更新蓝图

调用 `/blueprint --update`：

1. 更新功能进度
2. 更新测试覆盖率
3. 记录完成时间

> 参考: `workflows/blueprint.md`

---

## 完整流程图

```
                    ┌─────────────────┐
                    │    开始任务      │
                    └────────┬────────┘
                             │
                             ▼
        ┌────────────────────────────────────────┐
        │            阶段 1: TDD 开发              │
        │  RED → GREEN → REFACTOR → 覆盖率检查     │
        └────────────────────┬───────────────────┘
                             │
                             ▼
        ┌────────────────────────────────────────┐
        │            阶段 2: 代码审查              │
        │       多模型审查 → 问题汇总              │
        └────────────────────┬───────────────────┘
                             │
            ┌────────────────┼────────────────┐
            ▼                ▼                ▼
         通过 ✅          需修复           需干预 🛑
            │                │                │
            │                ▼                ▼
            │         修复 → 返回阶段2    生成报告
            │         (cycle < max)       暂停等待
            │                │
            │    cycle >= max│
            │                ▼
            │           需干预 🛑
            │
            ▼
        ┌────────────────────────────────────────┐
        │            阶段 3: 更新蓝图              │
        │         更新进度 → 记录完成              │
        └────────────────────┬───────────────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │    任务完成 ✅   │
                    └─────────────────┘
```

---

## 产出

- ✅ 测试完整的功能代码
- ✅ 通过多模型审查的代码
- ✅ 更新的项目蓝图
- ✅ Git 提交记录
