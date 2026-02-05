---
name: frontend-patterns
description: React + Inertia.js 开发模式、状态管理、性能优化和 UI 最佳实践。
---

# Frontend Development Patterns (React + Inertia.js)

针对 Laravel + Inertia.js 项目的现代前端开发模式。

## Inertia.js 核心模式

### 1. 访问 Page 数据 (usePage)

```typescript
import { usePage } from '@inertiajs/react'

export function UserProfile() {
  const { auth } = usePage().props
  
  return <div>Welcome, {auth.user.name}</div>
}
```

### 2. 导航与 Link

```typescript
import { Link } from '@inertiajs/react'

// ✅ 推荐：使用 Link 组件进行内部导航（避免全页刷新）
<Link href={route('profile.edit')} method="get" as="button">
  Edit Profile
</Link>
```

### 3. 表单处理 (useForm)

```typescript
import { useForm } from '@inertiajs/react'

export function CreatePost() {
  const { data, setData, post, processing, errors } = useForm({
    title: '',
    body: '',
  })

  const submit = (e) => {
    e.preventDefault()
    post(route('posts.store'))
  }

  return (
    <form onSubmit={submit}>
      <input 
        value={data.title} 
        onChange={e => setData('title', e.target.value)} 
      />
      {errors.title && <div>{errors.title}</div>}
      
      <button disabled={processing}>Submit</button>
    </form>
  )
}
```

## 组件模式

### 1. 基础布局集成 (Layouts)

```typescript
import Layout from '@/Layouts/AuthenticatedLayout'

const Dashboard = (props) => {
  return (
    <div>My Content</div>
  )
}

Dashboard.layout = page => <Layout children={page} title="Dashboard" />

export default Dashboard
```

### 2. 组件复合 (Composition)

```typescript
// ✅ 灵活的复合组件
export function Card({ children }) {
  return <div className="card">{children}</div>
}

Card.Header = ({ title }) => <div className="header">{title}</div>
Card.Body = ({ children }) => <div className="body">{children}</div>

// 使用
<Card>
  <Card.Header title="Title" />
  <Card.Body>Content</Card.Body>
</Card>
```

## 性能优化

### 1. Inertia 局部重新加载

```typescript
// 仅重新加载指定数据，不刷新整个页面
router.reload({ only: ['users'] })
```

### 2. React.memo 与 useMemo

```typescript
// ✅ 对于纯展示组件使用 memo
const MarketCard = React.memo(({ market }) => {
  return <div>{market.name}</div>
})

// ✅ 对昂贵计算使用 useMemo
const sortedItems = useMemo(() => items.sort(), [items])
```

## 表单与验证

### 1. 客户端预验证
在 `useForm` 的 `onBefore` 钩子中执行。

### 2. 服务器端错误处理
Inertia 会自动将 Laravel 的验证错误映射到 `useForm` 的 `errors` 对象。

## 动画模式 (Framer Motion)

```typescript
import { motion, AnimatePresence } from 'framer-motion'

export function List({ items }) {
  return (
    <AnimatePresence>
      {items.map(item => (
        <motion.div
          key={item.id}
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
        >
          {item.name}
        </motion.div>
      ))}
    </AnimatePresence>
  )
}
```

**提示**: 保持 UI 组件的小型化和单一职责，利用 Inertia.js 提供的钩子简化数据流。
