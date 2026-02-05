---
description: 生成或更新项目蓝图
---

# /blueprint 命令

生成项目蓝图，跟踪开发进度和质量指标。

## 用法

```
/blueprint [options]
```

## 选项

- `--update` - 更新现有蓝图而非重新生成
- `--focus=<module>` - 仅分析指定模块
- `--output=<path>` - 指定输出路径（默认 `blueprint.md`）

## 执行流程

1. 调用 `workflows/blueprint.md` 工作流
2. 扫描项目结构
3. 分析测试覆盖率
4. 评估代码质量
5. 生成/更新蓝图文档

## 示例

```bash
# 生成完整蓝图
/blueprint

# 更新蓝图
/blueprint --update

# 分析特定模块
/blueprint --focus=orders
```

## 输出

在项目根目录生成 `blueprint.md` 文件。
