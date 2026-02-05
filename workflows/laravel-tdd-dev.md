---
description: Laravel TDD 开发工作流，严格遵循测试驱动开发
---

# Laravel TDD 开发工作流

严格遵循 RED-GREEN-REFACTOR 循环的测试驱动开发流程。

## 前置条件

- 明确的功能需求
- 已确定测试范围（单元/功能/浏览器）

---

## 步骤 1: 需求分析

解析功能需求：
- 确定涉及的模型、控制器、服务
- 确定 API 端点或页面路由
- 确定验证规则
- 确定边界条件

---

## 步骤 2: 创建测试文件 (RED)
// turbo

根据功能类型创建测试：

**功能测试**（HTTP 请求）：
```bash
php artisan make:test {Feature}Test
```

**单元测试**（独立类）：
```bash
php artisan make:test {Class}Test --unit
```

---

## 步骤 3: 编写失败测试 (RED)

编写测试用例覆盖：
- ✅ 正常流程
- ❌ 验证失败
- 🔐 权限控制
- 🔄 边界条件

示例：
```php
public function test_user_can_create_product(): void
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->post('/products', [
            'name' => '测试产品',
            'price' => 99.99,
        ]);
    
    $response->assertRedirect('/products');
    $this->assertDatabaseHas('products', ['name' => '测试产品']);
}

public function test_product_requires_name(): void
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->post('/products', ['price' => 99.99]);
    
    $response->assertSessionHasErrors('name');
}
```

---

## 步骤 4: 验证测试失败
// turbo

运行测试确认失败：
```bash
php artisan test --filter={测试名}
```

确保失败原因是「功能未实现」而非测试问题。

---

## 步骤 5: 最小实现 (GREEN)

编写最小代码使测试通过：
- 创建必要的 Model、Controller、Request
- 实现核心逻辑
- 不要过度设计

---

## 步骤 6: 验证测试通过
// turbo

运行测试确认通过：
```bash
php artisan test --filter={测试名}
```

---

## 步骤 7: 重构 (REFACTOR)

优化代码结构：
- 提取服务类
- 消除重复
- 改进命名
- 添加注释

每次重构后运行测试确保仍通过。

---

## 步骤 8: 覆盖率检查
// turbo

检查测试覆盖率：
```bash
php artisan test --coverage --min=80
```

未达 80% 则补充测试：
- 遗漏的分支
- 未测试的方法
- 边界条件

---

## 步骤 9: 前端测试（可选）

**条件**: 功能涉及 Web 页面且使用 Antigravity 编辑器

使用浏览器工具执行自动化测试：
1. 打开目标页面
2. 执行用户操作流程
3. 验证页面状态
4. 截图记录结果

---

## 步骤 10: 移动端测试（可选）

**条件**: 目标平台为移动 App

调用 Maestro MCP 执行测试：
```yaml
# maestro-test.yaml
- launchApp
- tapOn: "登录"
- inputText: 
    id: "email"
    text: "test@example.com"
- tapOn: "提交"
- assertVisible: "欢迎"
```

---

## 步骤 11: 提交代码
// turbo

提交通过测试的代码：
```bash
git add .
git commit -m "feat: {功能描述}"
```

非 main/master 分支自动 push：
```bash
git push origin {当前分支}
```

---

## 测试类型矩阵

| 测试类型 | 工具 | 触发条件 | 目标覆盖率 |
|---------|------|---------|-----------|
| 单元测试 | PHPUnit/Pest | 必须 | 80%+ |
| 功能测试 | Laravel HTTP Tests | 有路由/控制器 | 80%+ |
| 浏览器测试 | Antigravity 浏览器 | Web 页面 | 关键流程 |
| 移动端测试 | Maestro MCP | 移动 App | 关键流程 |

---

## 产出

- ✅ 测试文件：`tests/Feature/{Test}.php` 或 `tests/Unit/{Test}.php`
- ✅ 功能代码：控制器、服务、模型等
- ✅ 覆盖率报告：80%+ 覆盖率
- ✅ Git 提交：带有描述性 commit message
