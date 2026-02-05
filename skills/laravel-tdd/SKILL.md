---
name: laravel-tdd
description: Laravel 测试驱动开发，PHPUnit/Pest 测试模式，Feature/Unit 测试最佳实践
---

# Laravel TDD 开发

Laravel 测试驱动开发工作流和测试模式。

## 激活时机

- 开发新功能（先写测试）
- 修复 Bug（先写失败测试）
- 重构代码（确保测试覆盖）

## TDD 循环

### 1. RED - 编写失败测试

```php
// tests/Feature/ProductTest.php
public function test_user_can_create_product(): void
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->post('/products', [
            'name' => '测试产品',
            'price' => 99.99,
            'category_id' => Category::factory()->create()->id,
        ]);
    
    $response->assertRedirect('/products');
    $this->assertDatabaseHas('products', [
        'name' => '测试产品',
        'price' => 99.99,
    ]);
}
```

### 2. GREEN - 最小实现

```php
// app/Http/Controllers/ProductController.php
public function store(StoreProductRequest $request): RedirectResponse
{
    Product::create($request->validated());
    return redirect('/products');
}
```

### 3. REFACTOR - 优化

运行测试确保仍然通过后优化代码。

## 运行测试

```bash
# 运行所有测试
php artisan test

# 运行特定测试
php artisan test --filter=ProductTest

# 带覆盖率
php artisan test --coverage --min=80

# Pest 格式
php artisan test --parallel
```

## Feature 测试

### HTTP 测试

```php
public function test_products_page_shows_products(): void
{
    $products = Product::factory()->count(3)->create();
    
    $response = $this->get('/products');
    
    $response->assertStatus(200);
    $response->assertInertia(fn ($page) => 
        $page->component('Products/Index')
            ->has('products', 3)
    );
}
```

### 认证测试

```php
public function test_guests_cannot_create_products(): void
{
    $response = $this->post('/products', [
        'name' => '测试产品',
        'price' => 99.99,
    ]);
    
    $response->assertRedirect('/login');
}

public function test_authenticated_users_can_create_products(): void
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->post('/products', [
            'name' => '测试产品',
            'price' => 99.99,
            'category_id' => Category::factory()->create()->id,
        ]);
    
    $response->assertRedirect('/products');
}
```

### 验证测试

```php
public function test_product_requires_name(): void
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->post('/products', [
            'price' => 99.99,
        ]);
    
    $response->assertSessionHasErrors('name');
}
```

## Unit 测试

### 模型测试

```php
public function test_product_belongs_to_category(): void
{
    $category = Category::factory()->create();
    $product = Product::factory()->create(['category_id' => $category->id]);
    
    $this->assertInstanceOf(Category::class, $product->category);
    $this->assertEquals($category->id, $product->category->id);
}

public function test_product_has_formatted_price(): void
{
    $product = Product::factory()->create(['price' => 99.99]);
    
    $this->assertEquals('¥99.99', $product->formatted_price);
}
```

### Service 测试

```php
public function test_order_service_creates_order(): void
{
    $user = User::factory()->create();
    $product = Product::factory()->create(['price' => 100]);
    
    $service = new OrderService();
    $order = $service->create([
        'items' => [
            ['product_id' => $product->id, 'quantity' => 2]
        ]
    ], $user);
    
    $this->assertEquals(200, $order->total);
    $this->assertCount(1, $order->items);
}
```

## Mocking

### Mock 外部服务

```php
public function test_payment_is_processed(): void
{
    $paymentGateway = $this->mock(PaymentGateway::class);
    $paymentGateway->shouldReceive('charge')
        ->once()
        ->with(100)
        ->andReturn(true);
    
    $service = app(OrderService::class);
    $result = $service->processPayment($order);
    
    $this->assertTrue($result);
}
```

### Mock 队列

```php
public function test_order_job_is_dispatched(): void
{
    Queue::fake();
    
    $this->actingAs($user)
        ->post('/orders', $orderData);
    
    Queue::assertPushed(ProcessOrderJob::class);
}
```

### Mock 事件

```php
public function test_order_created_event_is_fired(): void
{
    Event::fake();
    
    $service = new OrderService();
    $service->create($data, $user);
    
    Event::assertDispatched(OrderCreated::class);
}
```

## 数据库测试

### RefreshDatabase

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class ProductTest extends TestCase
{
    use RefreshDatabase;
    
    // 每个测试前重置数据库
}
```

### DatabaseTransactions

```php
use Illuminate\Foundation\Testing\DatabaseTransactions;

class ProductTest extends TestCase
{
    use DatabaseTransactions;
    
    // 每个测试后回滚事务（更快）
}
```

## Factory 模式

```php
// database/factories/ProductFactory.php
class ProductFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->words(3, true),
            'price' => fake()->randomFloat(2, 10, 1000),
            'category_id' => Category::factory(),
            'is_active' => true,
        ];
    }

    public function inactive(): static
    {
        return $this->state(['is_active' => false]);
    }
}

// 使用
Product::factory()->create();
Product::factory()->inactive()->create();
Product::factory()->count(5)->create();
```

## 覆盖率目标

- **总体覆盖率**: 80%+
- **关键路径**: 100%
- **Service 层**: 90%+
- **Controller 层**: 85%+

```bash
# 生成覆盖率报告
php artisan test --coverage --coverage-html=storage/coverage
```

## Pest 语法

```php
test('user can create product', function () {
    $user = User::factory()->create();
    
    $this->actingAs($user)
        ->post('/products', [
            'name' => '测试产品',
            'price' => 99.99,
        ])
        ->assertRedirect('/products');
    
    expect(Product::count())->toBe(1);
});

it('validates product name is required', function () {
    $user = User::factory()->create();
    
    $this->actingAs($user)
        ->post('/products', ['price' => 99.99])
        ->assertSessionHasErrors('name');
});
```

## 快速参考

| 测试类型 | 目录 | 用途 |
|---------|------|------|
| Feature | tests/Feature/ | HTTP 请求、完整流程 |
| Unit | tests/Unit/ | 单个类、方法 |

**记住**：先写测试，再写代码。测试不是可选的。
