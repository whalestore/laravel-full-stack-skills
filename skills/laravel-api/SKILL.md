---
name: laravel-api
description: Laravel API 开发模式，API Resource、Sanctum 认证、版本控制和错误处理
---

# Laravel API 开发

RESTful API 设计和实现最佳实践。

## 激活时机

- 构建 API 端点
- 设计 API 响应格式
- 实现 API 认证
- 处理 API 错误

## API 路由

### 路由定义

```php
// routes/api.php
Route::prefix('v1')->group(function () {
    // 公开路由
    Route::post('login', [AuthController::class, 'login']);
    Route::post('register', [AuthController::class, 'register']);
    
    // 需要认证
    Route::middleware('auth:sanctum')->group(function () {
        Route::apiResource('products', ProductController::class);
        Route::post('orders', [OrderController::class, 'store']);
    });
});
```

### 资源控制器

```php
class ProductController extends Controller
{
    public function index(): AnonymousResourceCollection
    {
        $products = Product::with('category')
            ->paginate(15);
        
        return ProductResource::collection($products);
    }

    public function store(StoreProductRequest $request): ProductResource
    {
        $product = Product::create($request->validated());
        
        return new ProductResource($product);
    }

    public function show(Product $product): ProductResource
    {
        return new ProductResource($product->load('category', 'tags'));
    }
}
```

## API Resource

### 基本资源

```php
class ProductResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'price' => $this->price,
            'description' => $this->description,
            'category' => new CategoryResource($this->whenLoaded('category')),
            'tags' => TagResource::collection($this->whenLoaded('tags')),
            'created_at' => $this->created_at->toISOString(),
            'updated_at' => $this->updated_at->toISOString(),
        ];
    }
}
```

### 条件字段

```php
public function toArray(Request $request): array
{
    return [
        'id' => $this->id,
        'name' => $this->name,
        // 仅管理员可见
        'cost' => $this->when($request->user()?->isAdmin(), $this->cost),
        // 仅加载时包含
        'orders' => OrderResource::collection($this->whenLoaded('orders')),
        // 仅存在时包含
        'average_rating' => $this->when($this->average_rating !== null, $this->average_rating),
    ];
}
```

## Sanctum 认证

### 配置

```php
// config/sanctum.php
'expiration' => 60 * 24 * 30, // 30天
```

### 登录

```php
class AuthController extends Controller
{
    public function login(LoginRequest $request): JsonResponse
    {
        $user = User::where('email', $request->email)->first();
        
        if (!$user || !Hash::check($request->password, $user->password)) {
            return response()->json([
                'message' => '凭证无效'
            ], 401);
        }
        
        $token = $user->createToken(
            $request->device_name ?? 'default',
            ['*'],
            now()->addDays(30)
        );
        
        return response()->json([
            'user' => new UserResource($user),
            'token' => $token->plainTextToken,
        ]);
    }

    public function logout(Request $request): JsonResponse
    {
        $request->user()->currentAccessToken()->delete();
        
        return response()->json(['message' => '已登出']);
    }
}
```

## 响应格式

### 成功响应

```php
// 单个资源
return new ProductResource($product);

// 集合
return ProductResource::collection($products);

// 自定义
return response()->json([
    'success' => true,
    'data' => new ProductResource($product),
    'message' => '产品创建成功'
], 201);
```

### 错误响应

```php
// 验证错误（422）
{
    "message": "The given data was invalid.",
    "errors": {
        "name": ["名称不能为空"],
        "price": ["价格必须大于0"]
    }
}

// 认证错误（401）
{
    "message": "Unauthenticated."
}

// 未找到（404）
{
    "message": "产品不存在"
}
```

## 异常处理

### Handler 配置

```php
// bootstrap/app.php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->render(function (NotFoundHttpException $e, Request $request) {
        if ($request->is('api/*')) {
            return response()->json([
                'message' => '资源不存在'
            ], 404);
        }
    });
    
    $exceptions->render(function (ModelNotFoundException $e, Request $request) {
        if ($request->is('api/*')) {
            return response()->json([
                'message' => '资源不存在'
            ], 404);
        }
    });
})
```

## 分页

### 自动分页

```php
public function index(): AnonymousResourceCollection
{
    return ProductResource::collection(
        Product::paginate(15)
    );
}
```

### 响应格式

```json
{
    "data": [...],
    "links": {
        "first": "http://api.example.com/products?page=1",
        "last": "http://api.example.com/products?page=10",
        "prev": null,
        "next": "http://api.example.com/products?page=2"
    },
    "meta": {
        "current_page": 1,
        "last_page": 10,
        "per_page": 15,
        "total": 150
    }
}
```

## Rate Limiting

### 配置

```php
// bootstrap/app.php
->withMiddleware(function (Middleware $middleware) {
    $middleware->api([
        'throttle:api',
    ]);
})

// app/Providers/AppServiceProvider.php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});
```

## 版本控制

### 路由前缀

```php
// routes/api.php
Route::prefix('v1')->group(function () {
    Route::apiResource('products', V1\ProductController::class);
});

Route::prefix('v2')->group(function () {
    Route::apiResource('products', V2\ProductController::class);
});
```

### 控制器命名空间

```
app/Http/Controllers/Api/
├── V1/
│   └── ProductController.php
└── V2/
    └── ProductController.php
```

## API 测试

```php
public function test_can_list_products(): void
{
    $products = Product::factory()->count(3)->create();
    
    $response = $this->getJson('/api/v1/products');
    
    $response
        ->assertStatus(200)
        ->assertJsonCount(3, 'data')
        ->assertJsonStructure([
            'data' => [
                '*' => ['id', 'name', 'price']
            ],
            'meta' => ['current_page', 'total']
        ]);
}

public function test_requires_authentication(): void
{
    $response = $this->postJson('/api/v1/products', [
        'name' => '测试产品'
    ]);
    
    $response->assertStatus(401);
}
```

## 快速参考

| 状态码 | 用途 |
|-------|------|
| 200 | 成功（GET, PUT） |
| 201 | 创建成功（POST） |
| 204 | 删除成功（DELETE） |
| 400 | 请求错误 |
| 401 | 未认证 |
| 403 | 未授权 |
| 404 | 未找到 |
| 422 | 验证失败 |
| 429 | 请求过多 |
| 500 | 服务器错误 |
