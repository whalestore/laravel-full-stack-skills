---
name: shadcn-ui
description: Shadcn/UI 组件库集成指南，适用于 Laravel + Inertia.js + React 项目的现代 SaaS 后台界面开发。
---

# Shadcn/UI 技能

为 Laravel + Inertia.js + React 项目提供 Shadcn/UI 组件集成的最佳实践。

## 激活时机

- 创建新的 SaaS 后台界面
- 开发管理面板组件
- 构建表单、表格、对话框等 UI 元素
- 需要一致的设计系统

## 安装配置

### 1. 初始化 Shadcn/UI

```bash
npx shadcn@latest init
```

配置选项：
- **Style**: Default 或 New York
- **Base color**: Slate / Gray / Zinc / Neutral / Stone
- **CSS variables**: Yes (推荐)
- **Tailwind CSS**: 必须已安装

### 2. 目录结构

```
resources/js/
├── components/
│   └── ui/              # Shadcn 组件（自动生成）
│       ├── button.tsx
│       ├── card.tsx
│       ├── input.tsx
│       └── ...
├── lib/
│   └── utils.ts         # cn() 工具函数
└── Pages/               # Inertia 页面
```

## 常用组件安装

```bash
# 表单组件
npx shadcn@latest add button input label form

# 数据展示
npx shadcn@latest add table card badge

# 反馈组件
npx shadcn@latest add dialog alert toast

# 导航组件
npx shadcn@latest add dropdown-menu tabs navigation-menu

# 布局组件
npx shadcn@latest add separator sheet sidebar
```

## 组件使用模式

### 1. 基础按钮

```tsx
import { Button } from "@/components/ui/button"

// 变体
<Button variant="default">默认</Button>
<Button variant="destructive">删除</Button>
<Button variant="outline">轮廓</Button>
<Button variant="ghost">幽灵</Button>

// 尺寸
<Button size="sm">小</Button>
<Button size="lg">大</Button>

// 加载状态
<Button disabled={loading}>
  {loading && <Loader2 className="animate-spin" />}
  提交
</Button>
```

### 2. 表单与验证（结合 Inertia useForm）

```tsx
import { useForm } from '@inertiajs/react'
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

export function CreateForm() {
  const { data, setData, post, processing, errors } = useForm({
    name: '',
    email: '',
  })

  return (
    <form onSubmit={e => { e.preventDefault(); post('/users') }}>
      <div className="space-y-4">
        <div className="space-y-2">
          <Label htmlFor="name">名称</Label>
          <Input 
            id="name"
            value={data.name}
            onChange={e => setData('name', e.target.value)}
            className={errors.name ? 'border-destructive' : ''}
          />
          {errors.name && <p className="text-sm text-destructive">{errors.name}</p>}
        </div>
        
        <Button type="submit" disabled={processing}>
          创建
        </Button>
      </div>
    </form>
  )
}
```

### 3. 数据表格

```tsx
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table"

export function UserTable({ users }) {
  return (
    <Table>
      <TableHeader>
        <TableRow>
          <TableHead>名称</TableHead>
          <TableHead>邮箱</TableHead>
          <TableHead className="text-right">操作</TableHead>
        </TableRow>
      </TableHeader>
      <TableBody>
        {users.map(user => (
          <TableRow key={user.id}>
            <TableCell className="font-medium">{user.name}</TableCell>
            <TableCell>{user.email}</TableCell>
            <TableCell className="text-right">
              <Button variant="ghost" size="sm">编辑</Button>
            </TableCell>
          </TableRow>
        ))}
      </TableBody>
    </Table>
  )
}
```

### 4. 对话框确认

```tsx
import {
  AlertDialog,
  AlertDialogAction,
  AlertDialogCancel,
  AlertDialogContent,
  AlertDialogDescription,
  AlertDialogFooter,
  AlertDialogHeader,
  AlertDialogTitle,
  AlertDialogTrigger,
} from "@/components/ui/alert-dialog"

export function DeleteConfirm({ onConfirm }) {
  return (
    <AlertDialog>
      <AlertDialogTrigger asChild>
        <Button variant="destructive">删除</Button>
      </AlertDialogTrigger>
      <AlertDialogContent>
        <AlertDialogHeader>
          <AlertDialogTitle>确认删除？</AlertDialogTitle>
          <AlertDialogDescription>
            此操作无法撤销，数据将被永久删除。
          </AlertDialogDescription>
        </AlertDialogHeader>
        <AlertDialogFooter>
          <AlertDialogCancel>取消</AlertDialogCancel>
          <AlertDialogAction onClick={onConfirm}>确认删除</AlertDialogAction>
        </AlertDialogFooter>
      </AlertDialogContent>
    </AlertDialog>
  )
}
```

## SaaS 后台布局模式

### 推荐布局结构

```tsx
// Layouts/DashboardLayout.tsx
import { SidebarProvider, Sidebar, SidebarContent } from "@/components/ui/sidebar"

export function DashboardLayout({ children }) {
  return (
    <SidebarProvider>
      <div className="flex h-screen">
        <Sidebar>
          <SidebarContent>
            {/* 导航菜单 */}
          </SidebarContent>
        </Sidebar>
        <main className="flex-1 overflow-auto p-6">
          {children}
        </main>
      </div>
    </SidebarProvider>
  )
}
```

## 设计令牌

Shadcn 使用 CSS 变量，可在 `globals.css` 中自定义：

```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --primary: 222.2 47.4% 11.2%;
  --primary-foreground: 210 40% 98%;
  /* 更多变量... */
}

.dark {
  --background: 222.2 84% 4.9%;
  --foreground: 210 40% 98%;
  /* 暗色模式... */
}
```

## 与 Inertia.js 集成要点

1. **表单**: 使用 `useForm` 钩子管理状态，Shadcn 组件负责 UI
2. **导航**: 使用 `Link` 组件而非 `<a>` 标签
3. **闪存消息**: 结合 `usePage().props.flash` 与 `toast` 组件
4. **加载状态**: 利用 `processing` 状态禁用按钮

---

*基于 shadcn/ui 最新版本，适用于 React 18+ 和 Tailwind CSS 3+。*
