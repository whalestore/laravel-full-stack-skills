---
name: multi-model-review
description: 多模型代码审查配置和协调
---

# 多模型代码审查

配置和使用多个 AI 模型进行代码审查。

## 激活时机

- 需要代码审查时
- 配置审查模型时
- 自定义审查规则时

## 模型配置

### 可用模型

| 模型 | 关注点 | 适用场景 |
|-----|-------|---------|
| Gemini | 架构、性能 | 大型变更、重构 |
| Codex | 安全、最佳实践 | 核心功能、API |
| 智谱 | 代码风格、文档 | 日常开发 |

### 配置文件

在项目根目录创建 `.ai/review-config.yaml`：

```yaml
models:
  gemini:
    enabled: true
    focus:
      - architecture
      - performance
      - complexity
    threshold: medium
    
  codex:
    enabled: true
    focus:
      - security
      - best-practices
      - error-handling
    threshold: low
    
  zhipu:
    enabled: true
    focus:
      - code-style
      - documentation
      - naming
    threshold: low

rules:
  max_file_size: 500  # 行数
  ignore_patterns:
    - "*.min.js"
    - "vendor/*"
    - "node_modules/*"
```

## 审查模板

### 架构审查（Gemini）

```markdown
## 架构审查

请分析以下代码的架构设计：

### 检查项
1. 类职责是否单一
2. 依赖关系是否合理
3. 是否有循环依赖
4. 是否过度设计

### 代码
{code}

### 期望输出
- 问题列表（包含位置和建议）
- 改进建议
```

### 安全审查（Codex）

```markdown
## 安全审查

请检查以下代码的安全性：

### 检查项
1. SQL 注入
2. XSS 漏洞
3. CSRF 保护
4. 权限验证
5. 敏感数据处理

### 代码
{code}

### 期望输出
- 安全问题列表（包含严重性）
- 修复代码建议
```

### 规范审查（智谱）

```markdown
## 代码规范审查

请检查以下代码的规范性：

### 检查项
1. PSR-12 代码风格
2. 命名规范
3. 注释完整性
4. 代码可读性

### 代码
{code}

### 期望输出
- 规范问题列表
- 改进建议
```

## 问题分级标准

| 级别 | 定义 | 示例 |
|-----|------|------|
| Critical | 安全漏洞、数据丢失 | SQL 注入、未加密存储密码 |
| High | 功能缺陷、性能问题 | N+1 查询、逻辑错误 |
| Medium | 代码质量问题 | 代码重复、复杂度高 |
| Low | 建议和优化 | 命名改进、注释补充 |

## 使用流程

1. **收集变更** - 获取待审查文件列表
2. **并行审查** - 同时调用配置的模型
3. **汇总结果** - 合并去重，按优先级排序
4. **生成报告** - 输出结构化审查报告
