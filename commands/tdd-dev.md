---
description: 启动 TDD 开发流程
---

# /tdd-dev 命令

使用测试驱动开发方式实现功能。

## 用法

```
/tdd-dev <功能描述>
```

## 选项

- `--skip-browser` - 跳过浏览器测试
- `--skip-mobile` - 跳过移动端测试
- `--min-coverage=<n>` - 覆盖率阈值（默认 80）

## 执行流程

1. 调用 `workflows/laravel-tdd-dev.md` 工作流
2. 分析需求 → 编写测试 → 实现代码 → 重构
3. 验证覆盖率
4. 提交代码

## 示例

```bash
# 开发产品创建功能
/tdd-dev 实现产品创建功能

# 跳过浏览器测试
/tdd-dev 实现 API 端点 --skip-browser

# 自定义覆盖率要求
/tdd-dev 实现订单取消 --min-coverage=90
```

## TDD 循环

```
RED ──→ GREEN ──→ REFACTOR ──→ COMMIT
 │                               │
 └───────────────────────────────┘
         下一个测试用例
```
