---
description: 启动代码审查流程
---

# /code-review 命令

启动多模型代码审查，自动修复问题。

## 用法

```
/code-review [options]
```

## 选项

- `--range=<ref>` - 指定比较范围（默认 HEAD~1）
- `--files=<paths>` - 指定审查文件
- `--max-retries=<n>` - 最大自动修复次数（默认 3）
- `--no-auto-fix` - 禁用自动修复
- `--models=<list>` - 指定审查模型（默认全部）

## 执行流程

1. 调用 `workflows/code-review.md` 工作流
2. 并行调用多个审查模型
3. 汇总问题并按优先级排序
4. 自动修复循环（如启用）
5. 生成审查报告

## 示例

```bash
# 审查最近一次提交
/code-review

# 审查与 main 分支的差异
/code-review --range=main..HEAD

# 仅使用 Gemini 审查
/code-review --models=gemini

# 禁用自动修复
/code-review --no-auto-fix
```

## 审查结果

| 状态 | 含义 |
|-----|------|
| ✅ 通过 | 无问题或仅低优先级建议 |
| 🔄 修复中 | 正在自动修复 |
| 🛑 需干预 | 需要人工决策 |
