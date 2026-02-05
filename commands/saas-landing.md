---
description: 从 Stitch 设计生成 SaaS 主页
---

# /saas-landing 命令

从 Stitch 设计项目生成完整的 SaaS 主页。

## 用法

```bash
/saas-landing [选项]
```

## 选项

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--design <路径>` | Stitch 设计目录或文件 | `./stitch` |
| `--output <路径>` | 输出组件目录 | `resources/js/components/landing` |
| `--page <路径>` | 主页文件路径 | `resources/js/Pages/Welcome.tsx` |
| `--video` | 生成演示视频 | `false` |
| `--preview` | 生成后自动预览 | `true` |

## 示例

```bash
# 默认：从 ./stitch 目录生成
/saas-landing

# 指定设计目录
/saas-landing --design ./designs/landing-v2

# 生成并录制演示视频
/saas-landing --video

# 自定义输出路径
/saas-landing --output resources/js/components/home --page resources/js/Pages/Home.tsx
```

## 执行流程

1. **识别设计** → 扫描 Stitch 设计文件
2. **生成文档** → 创建/更新 DESIGN.md
3. **增强提示** → 优化 UI 生成提示词
4. **生成组件** → 转换为 React 组件
5. **组装页面** → 创建完整主页
6. **验证** → 自动预览和响应式检查
7. **视频** (可选) → 生成 Remotion 演示视频

## 前置要求

安装 Stitch 技能:

```bash
npx skills add google-labs-code/stitch-skills --skill design-md --global
npx skills add google-labs-code/stitch-skills --skill react:components --global
npx skills add google-labs-code/stitch-skills --skill stitch-loop --global
npx skills add google-labs-code/stitch-skills --skill enhance-prompt --global
npx skills add google-labs-code/stitch-skills --skill remotion --global
```

## 输出

| 文件 | 说明 |
|------|------|
| `components/landing/*.tsx` | 主页组件 (Hero, Features, Pricing...) |
| `Pages/Welcome.tsx` | 组装后的完整主页 |
| `DESIGN.md` | 设计系统文档 |
| `out/walkthrough.mp4` | 演示视频 (如启用) |

## 关联工作流

- `workflows/saas-landing.md` - 详细步骤
- `skills/shadcn-ui/SKILL.md` - UI 组件库
