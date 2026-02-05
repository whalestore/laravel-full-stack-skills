---
description: 从 Stitch 设计图生成 SaaS 主页
---

# SaaS 主页工作流

从 Stitch 设计项目读取设计稿，自动生成完整的 SaaS 主页代码。

## 前置条件

- 已安装 Stitch 相关技能:
  - `design-md` - 设计系统文档生成
  - `react:components` - React 组件转换
  - `stitch-loop` - 多页面生成
  - `enhance-prompt` - 提示词增强
  - `remotion` (可选) - 视频生成

- 项目目录包含 Stitch 设计文件 (.stitch 或设计图目录)

---

## 步骤 1: 识别设计资产

扫描项目中的设计文件:

```
stitch/
├── screens/
│   ├── hero.png
│   ├── features.png
│   ├── pricing.png
│   └── footer.png
├── design-tokens.json
└── DESIGN.md (如果存在)
```

---

## 步骤 2: 生成设计文档 (design-md)

如果 `DESIGN.md` 不存在，自动生成:

```markdown
# Design System

## Colors
- Primary: #4F46E5
- Secondary: #10B981
- ...

## Typography
- Heading: Inter, 700
- Body: Inter, 400

## Spacing
- Base: 4px
- ...

## Components
- Hero Section
- Feature Cards
- Pricing Table
- ...
```

> 参考技能: `design-md`

---

## 步骤 3: 增强提示词 (enhance-prompt)

将简单描述转换为详细的 UI 生成提示:

**输入**: "SaaS landing page for project management tool"

**输出**:
```
A modern SaaS landing page for a project management tool featuring:
- Hero section with gradient background, bold headline, and CTA button
- Feature grid with 3x2 icon cards
- Pricing table with 3 tiers (Starter, Pro, Enterprise)
- Testimonial section with avatar carousel
- Footer with navigation links and newsletter signup
Design style: Clean, professional, using Inter font family
Color scheme: Deep blue primary (#1E40AF), vibrant accent (#06B6D4)
```

> 参考技能: `enhance-prompt`

---

## 步骤 4: 生成 React 组件 (react:components)

将每个设计屏幕转换为 React 组件:

```
resources/js/
├── components/
│   └── landing/
│       ├── HeroSection.tsx
│       ├── FeatureGrid.tsx
│       ├── PricingTable.tsx
│       ├── Testimonials.tsx
│       └── Footer.tsx
└── Pages/
    └── Welcome.tsx  # 组合所有组件
```

### 组件模板

```tsx
// resources/js/components/landing/HeroSection.tsx
export function HeroSection() {
  return (
    <section className="relative py-20 bg-gradient-to-br from-primary to-primary/80">
      <div className="container mx-auto px-4">
        <h1 className="text-5xl font-bold text-white">
          {/* 从设计稿提取 */}
        </h1>
        <p className="mt-6 text-xl text-white/80">
          {/* 从设计稿提取 */}
        </p>
        <Button size="lg" className="mt-8">
          Get Started Free
        </Button>
      </div>
    </section>
  )
}
```

> 参考技能: `react:components`

---

## 步骤 5: 组装页面 (stitch-loop)

创建完整的 Welcome 页面:

```tsx
// resources/js/Pages/Welcome.tsx
import { HeroSection } from '@/components/landing/HeroSection'
import { FeatureGrid } from '@/components/landing/FeatureGrid'
import { PricingTable } from '@/components/landing/PricingTable'
import { Testimonials } from '@/components/landing/Testimonials'
import { Footer } from '@/components/landing/Footer'

export default function Welcome() {
  return (
    <main>
      <HeroSection />
      <FeatureGrid />
      <PricingTable />
      <Testimonials />
      <Footer />
    </main>
  )
}
```

> 参考技能: `stitch-loop`

---

## 步骤 6: 验证与调整

// turbo
1. 运行开发服务器: `npm run dev`
2. 浏览器打开验证 UI
3. 如有偏差，调整组件样式
4. 响应式检查 (移动端/平板/桌面)

---

## 步骤 7 (可选): 生成演示视频 (remotion)

使用 Remotion 生成产品演示视频:

```bash
npx remotion render walkthrough
```

输出: `out/walkthrough.mp4`

> 参考技能: `remotion`

---

## 输出

- `resources/js/components/landing/` - 主页组件
- `resources/js/Pages/Welcome.tsx` - 组装后的主页
- `DESIGN.md` - 设计系统文档 (如果新生成)
- `out/walkthrough.mp4` - 演示视频 (可选)

---

## 与其他工作流集成

完成主页后:
1. 运行 `/code-review` 审查代码质量
2. 运行 `/blueprint --edit` 更新项目进度
