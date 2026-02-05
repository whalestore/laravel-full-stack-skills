---
name: laravel-patterns
description: Laravel 架构模式、控制器设计、服务层、Repository 模式和 Eloquent 最佳实践
---

# Laravel 开发模式

生产级 Laravel 应用架构模式和最佳实践。

## 激活时机

- 构建 Laravel 应用
- 设计项目结构
- 编写控制器和服务
- 使用 Eloquent ORM

## 项目结构

### 推荐布局

```
app/
├── Http/
│   ├── Controllers/
│   │   ├── Api/              # API 控制器
│   │   └── Web/              # Web 控制器
│   ├── Requests/             # Form Requests
│   ├── Resources/            # API Resources
│   └── Middleware/
├── Models/                   # Eloquent 模型
├── Services/                 # 业务逻辑层
├── Repositories/             # 数据访问层
├── Events/
├── Listeners/
├── Jobs/
└── Policies/
resources/
├── js/
│   ├── Pages/               # Inertia 页面
│   ├── Components/          # React 组件
│   └── Layouts/
├── views/
└── css/
tests/
├── Feature/
└── Unit/
```

## 控制器模式

### 资源控制器

```php
class ProductController extends Controller
{
    public function __construct(
        private ProductService $productService
    ) {}

    public function index(): Response
    {
        $products = $this->productService->getPaginated();
        
        return Inertia::render('Products/Index', [
            'products' => ProductResource::collection($products)
        ]);
    }

    public function store(StoreProductRequest $request): RedirectResponse
    {
        $product = $this->productService->create($request->validated());
        
        return redirect()
            ->route('products.show', $product)
            ->with('success', '产品创建成功');
    }
}
```

### 单一职责

```php
// 好：专注单一资源
class OrderController extends Controller
{
    public function store(StoreOrderRequest $request) { }
    public function show(Order $order) { }
}

// 好：独立操作用单独控制器
class OrderCancellationController extends Controller
{
    public function __invoke(Order $order): RedirectResponse
    {
        $this->orderService->cancel($order);
        return back()->with('success', '订单已取消');
    }
}
```

## 服务层模式

### 业务逻辑封装

```php
class OrderService
{
    public function __construct(
        private OrderRepository $orders,
        private PaymentGateway $payment,
        private NotificationService $notifications
    ) {}

    public function create(array $data, User $user): Order
    {
        return DB::transaction(function () use ($data, $user) {
            $order = $this->orders->create([
                'user_id' => $user->id,
                'total' => $this->calculateTotal($data['items']),
                'status' => OrderStatus::Pending,
            ]);

            foreach ($data['items'] as $item) {
                $order->items()->create($item);
            }

            $this->notifications->sendOrderConfirmation($order);

            return $order;
        });
    }

    public function cancel(Order $order): void
    {
        throw_unless(
            $order->canBeCancelled(),
            OrderCannotBeCancelledException::class
        );

        $order->update(['status' => OrderStatus::Cancelled]);
        $this->payment->refund($order);
        $this->notifications->sendCancellationNotice($order);
    }
}
```

## Form Request

### 验证封装

```php
class StoreProductRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Product::class);
    }

    public function rules(): array
    {
        return [
            'name' => ['required', 'string', 'max:255'],
            'price' => ['required', 'numeric', 'min:0'],
            'category_id' => ['required', 'exists:categories,id'],
            'description' => ['nullable', 'string'],
        ];
    }

    public function messages(): array
    {
        return [
            'name.required' => '产品名称不能为空',
        ];
    }
}
```

## Eloquent 模式

### 模型定义

```php
class Product extends Model
{
    protected $fillable = [
        'name',
        'slug',
        'price',
        'description',
        'category_id',
        'is_active',
    ];

    protected $casts = [
        'price' => 'decimal:2',
        'is_active' => 'boolean',
        'metadata' => 'array',
    ];

    // 关联（不使用外键约束）
    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }

    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class);
    }

    // 作用域
    public function scopeActive(Builder $query): Builder
    {
        return $query->where('is_active', true);
    }

    // 访问器
    protected function formattedPrice(): Attribute
    {
        return Attribute::get(fn () => '¥' . number_format($this->price, 2));
    }
}
```

### 避免 N+1 查询

```php
// 差：N+1 问题
$products = Product::all();
foreach ($products as $product) {
    echo $product->category->name;
}

// 好：预加载
$products = Product::with('category')->get();

// 好：嵌套预加载
$products = Product::with(['category', 'tags', 'reviews.user'])->get();
```

## API Resource

```php
class ProductResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'price' => $this->price,
            'formatted_price' => $this->formatted_price,
            'category' => new CategoryResource($this->whenLoaded('category')),
            'tags' => TagResource::collection($this->whenLoaded('tags')),
            'created_at' => $this->created_at->toISOString(),
        ];
    }
}
```

## 队列任务

```php
class ProcessOrderJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(
        public Order $order
    ) {}

    public function handle(PaymentGateway $payment): void
    {
        $payment->charge($this->order);
        $this->order->update(['status' => OrderStatus::Paid]);
    }

    public function failed(Throwable $exception): void
    {
        Log::error('订单处理失败', [
            'order_id' => $this->order->id,
            'error' => $exception->getMessage(),
        ]);
    }
}
```

## 事件和监听器

```php
// 事件
class OrderCreated
{
    public function __construct(
        public Order $order
    ) {}
}

// 监听器
class SendOrderNotification implements ShouldQueue
{
    public function handle(OrderCreated $event): void
    {
        $event->order->user->notify(
            new OrderConfirmationNotification($event->order)
        );
    }
}

// 触发
OrderCreated::dispatch($order);
```

## 快速参考

| 模式 | 用途 |
|------|------|
| Form Request | 请求验证和授权 |
| Service | 业务逻辑封装 |
| Repository | 数据访问抽象 |
| Resource | API 响应转换 |
| Job | 异步任务处理 |
| Event/Listener | 解耦业务操作 |
| Policy | 授权逻辑 |

**记住**：控制器保持精简，业务逻辑放入服务层，数据访问通过 Repository 或 Eloquent 作用域。
