---
name: auto-fix-loop
description: 自动修复循环逻辑和控制
---

# 自动修复循环

代码审查发现问题后的自动修复机制。

## 激活时机

- 代码审查发现问题
- 需要自动修复配置
- 调试修复循环

## 循环控制

### 配置参数

```yaml
auto_fix:
  enabled: true
  max_retries: 3
  retry_delay: 0  # 秒
  priority_threshold: medium  # low/medium/high/critical
  
  # 自动修复的问题类型
  fix_types:
    - code_style
    - simple_bug
    - performance
    - documentation
  
  # 需要人工确认的问题类型
  manual_types:
    - security_critical
    - architecture_change
    - breaking_change
    - data_migration
```

### 循环状态机

```
┌─────────────────────────────────────────────────────────────┐
│                         初始状态                             │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                         审查代码                             │
│                    (调用审查模型)                            │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         无问题         可修复问题       需人工干预
              │               │               │
              ▼               ▼               ▼
         ✅ 通过         修复代码        🛑 暂停
                              │         生成报告
                              ▼
                         运行测试
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         测试通过        测试失败       测试超时
              │               │               │
              ▼               ▼               ▼
         重新审查        回滚修复        🛑 暂停
              │          重试/暂停
              │               │
              └───────┬───────┘
                      ▼
              检查重试次数
              < max_retries
                      │
         ┌────────────┴────────────┐
         ▼                         ▼
    继续循环                   🛑 暂停
    返回审查                  请求人工介入
```

## 修复策略

### 代码风格问题

```yaml
type: code_style
strategy: auto
tools:
  - php-cs-fixer
  - prettier
commands:
  - "vendor/bin/php-cs-fixer fix {file}"
  - "npm run format -- {file}"
```

### 简单 Bug

```yaml
type: simple_bug
strategy: ai_suggest
process:
  1. 分析错误信息
  2. 生成修复代码
  3. 应用修复
  4. 运行相关测试
```

### 性能问题

```yaml
type: performance
strategy: ai_suggest
process:
  1. 识别问题模式（N+1 等）
  2. 生成优化代码
  3. 应用修复
  4. 验证性能改善
```

## 回滚机制

修复失败时自动回滚：

```bash
# 保存修复前状态
git stash

# 应用修复
apply_fixes()

# 测试失败则回滚
if test_failed:
    git stash pop
    retry_or_stop()
```

## 人工干预触发

以下情况停止自动修复，请求人工介入：

1. **超过重试限制** - retry_count >= max_retries
2. **Critical 问题** - 安全漏洞等
3. **架构变更** - 需要重新设计
4. **破坏性变更** - 影响 API 兼容性
5. **测试持续失败** - 无法通过测试

## 日志记录

每次循环记录：

```json
{
  "iteration": 1,
  "timestamp": "2024-01-15T10:30:00Z",
  "issues_found": 3,
  "issues_fixed": 2,
  "issues_remaining": 1,
  "test_result": "passed",
  "next_action": "review"
}
```

## 快速参考

| 参数 | 默认值 | 说明 |
|-----|--------|------|
| max_retries | 3 | 最大重试次数 |
| priority_threshold | medium | 自动修复阈值 |
| test_command | php artisan test | 测试命令 |
