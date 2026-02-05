---
name: inertia-react
description: Inertia.js + React 开发模式，页面组件、表单处理和与 Laravel 集成
---

# Inertia.js + React 开发

Laravel + Inertia.js + React 全栈开发模式。

## 激活时机

- 构建 Inertia 页面
- 处理表单提交
- 管理页面状态
- 与 Laravel 后端交互

## 项目结构

```
resources/js/
├── Pages/
│   ├── Products/
│   │   ├── Index.tsx
│   │   ├── Show.tsx
│   │   ├── Create.tsx
│   │   └── Edit.tsx
│   └── Dashboard.tsx
├── Components/
│   ├── UI/
│   ├── Forms/
│   └── Layout/
├── Layouts/
│   ├── AuthenticatedLayout.tsx
│   └── GuestLayout.tsx
├── Types/
│   └── index.d.ts
└── app.tsx
```

## 页面组件

### 基本页面

```tsx
import { Head } from '@inertiajs/react';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout';
import { Product, PageProps } from '@/Types';

interface Props extends PageProps {
    products: Product[];
}

export default function Index({ products, auth }: Props) {
    return (
        <AuthenticatedLayout user={auth.user}>
            <Head title="产品列表" />
            
            <div className="py-12">
                <div className="mx-auto max-w-7xl sm:px-6 lg:px-8">
                    {products.map((product) => (
                        <ProductCard key={product.id} product={product} />
                    ))}
                </div>
            </div>
        </AuthenticatedLayout>
    );
}
```

### 类型定义

```typescript
// Types/index.d.ts
export interface User {
    id: number;
    name: string;
    email: string;
}

export interface Product {
    id: number;
    name: string;
    price: number;
    description?: string;
    category?: Category;
}

export interface PageProps {
    auth: {
        user: User;
    };
    flash: {
        success?: string;
        error?: string;
    };
}
```

## 表单处理

### useForm Hook

```tsx
import { useForm } from '@inertiajs/react';

export default function CreateProduct() {
    const { data, setData, post, processing, errors, reset } = useForm({
        name: '',
        price: '',
        description: '',
        category_id: '',
    });

    const handleSubmit = (e: React.FormEvent) => {
        e.preventDefault();
        post('/products', {
            onSuccess: () => reset(),
        });
    };

    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label>名称</label>
                <input
                    type="text"
                    value={data.name}
                    onChange={(e) => setData('name', e.target.value)}
                />
                {errors.name && <span className="error">{errors.name}</span>}
            </div>

            <div>
                <label>价格</label>
                <input
                    type="number"
                    value={data.price}
                    onChange={(e) => setData('price', e.target.value)}
                />
                {errors.price && <span className="error">{errors.price}</span>}
            </div>

            <button type="submit" disabled={processing}>
                {processing ? '提交中...' : '创建产品'}
            </button>
        </form>
    );
}
```

### 编辑表单

```tsx
interface Props extends PageProps {
    product: Product;
}

export default function EditProduct({ product }: Props) {
    const { data, setData, put, processing, errors } = useForm({
        name: product.name,
        price: product.price.toString(),
        description: product.description || '',
    });

    const handleSubmit = (e: React.FormEvent) => {
        e.preventDefault();
        put(`/products/${product.id}`);
    };

    return (
        <form onSubmit={handleSubmit}>
            {/* 表单字段 */}
            <button type="submit" disabled={processing}>
                更新产品
            </button>
        </form>
    );
}
```

## 链接和导航

### Link 组件

```tsx
import { Link } from '@inertiajs/react';

// 基本链接
<Link href="/products">产品列表</Link>

// 带参数
<Link href={`/products/${product.id}`}>查看详情</Link>

// 替换历史
<Link href="/products" replace>产品列表</Link>

// 保留滚动位置
<Link href="/products" preserveScroll>产品列表</Link>
```

### router 方法

```tsx
import { router } from '@inertiajs/react';

// 导航
router.visit('/products');

// POST 请求
router.post('/products', data);

// 删除
router.delete(`/products/${id}`, {
    onBefore: () => confirm('确定删除？'),
});

// 重新加载
router.reload({ only: ['products'] });
```

## 共享数据

### Laravel 端

```php
// app/Http/Middleware/HandleInertiaRequests.php
public function share(Request $request): array
{
    return [
        ...parent::share($request),
        'auth' => [
            'user' => $request->user(),
        ],
        'flash' => [
            'success' => fn () => $request->session()->get('success'),
            'error' => fn () => $request->session()->get('error'),
        ],
    ];
}
```

### React 端

```tsx
import { usePage } from '@inertiajs/react';

export default function Notification() {
    const { flash } = usePage<PageProps>().props;
    
    return (
        <>
            {flash.success && (
                <div className="alert alert-success">{flash.success}</div>
            )}
            {flash.error && (
                <div className="alert alert-error">{flash.error}</div>
            )}
        </>
    );
}
```

## 布局

### 持久布局

```tsx
// Layouts/AuthenticatedLayout.tsx
import { PropsWithChildren } from 'react';
import { User } from '@/Types';
import Navbar from '@/Components/Navbar';
import Sidebar from '@/Components/Sidebar';

interface Props extends PropsWithChildren {
    user: User;
}

export default function AuthenticatedLayout({ user, children }: Props) {
    return (
        <div className="min-h-screen bg-gray-100">
            <Navbar user={user} />
            <div className="flex">
                <Sidebar />
                <main className="flex-1 p-6">{children}</main>
            </div>
        </div>
    );
}
```

### 使用布局

```tsx
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout';

export default function Dashboard({ auth }: Props) {
    return (
        <AuthenticatedLayout user={auth.user}>
            <h1>仪表盘</h1>
        </AuthenticatedLayout>
    );
}
```

## 文件上传

```tsx
import { useForm } from '@inertiajs/react';

export default function UploadForm() {
    const { data, setData, post, progress } = useForm({
        name: '',
        avatar: null as File | null,
    });

    const handleSubmit = (e: React.FormEvent) => {
        e.preventDefault();
        post('/profile', {
            forceFormData: true,
        });
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="file"
                onChange={(e) => setData('avatar', e.target.files?.[0] || null)}
            />
            {progress && (
                <progress value={progress.percentage} max="100">
                    {progress.percentage}%
                </progress>
            )}
            <button type="submit">上传</button>
        </form>
    );
}
```

## 测试 Inertia 响应

```php
public function test_products_page_renders(): void
{
    $products = Product::factory()->count(3)->create();
    
    $response = $this->actingAs($user)
        ->get('/products');
    
    $response
        ->assertStatus(200)
        ->assertInertia(fn ($page) => $page
            ->component('Products/Index')
            ->has('products', 3)
            ->has('products.0', fn ($page) => $page
                ->has('id')
                ->has('name')
                ->has('price')
            )
        );
}
```

## 快速参考

| Hook/方法 | 用途 |
|----------|------|
| `useForm` | 表单状态管理 |
| `usePage` | 访问共享数据 |
| `Link` | 客户端导航 |
| `router.visit` | 程序化导航 |
| `router.reload` | 部分重载 |
| `Head` | 页面标题/meta |

**记住**：Inertia 是 SPA 体验，服务端路由。保持组件简洁，业务逻辑在后端。
