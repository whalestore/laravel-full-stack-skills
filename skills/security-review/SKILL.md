---
name: security-review
description: 安全审查技能，涵盖 Laravel 安全实践、认证、授权、输入验证和常见漏洞防御。
---

# Security Review Skill (Laravel Focused)

确保代码符合 Laravel 安全最佳实践，识别潜在漏洞。

## Laravel 安全架构

### 1. 认证 (Sanctum/Auth)

- **Sanctum**: SPA (Inertia) 推荐使用第一方认证路由。
- **Password**: 始终使用 `Bcrypt`（Laravel 默认）。

### 2. 授权 (Policies/Gates)

```php
// ✅ 检查权限
public function update(User $user, Post $post)
{
    return $user->id === $post->user_id;
}

// 控制器中使用
$this->authorize('update', $post);
```

## 安全检查清单

### 1. 密钥管理 (.env)

- ❌ 禁止在代码中硬编码秘钥、令牌。
- ✅ 始终通过 `env()` 或 `config()` 访问配置。
- ✅ 确保 `.env` 不在 Git 目录中。

### 2. 输入验证 (Request Validation)

```php
// ✅ 定义 FormRequest
public function rules()
{
    return [
      'email' => 'required|email|unique:users',
      'password' => 'required|min:8|confirmed',
    ];
}
```

### 3. SQL 注入防御

- ✅ 使用 Eloquent 或查询构造器的参数绑定。
- ❌ 严禁直接拼接字符串到 `DB::raw()`。

```php
// ❌ 危险
DB::select("SELECT * FROM users WHERE email = '$email'");

// ✅ 安全
DB::select("SELECT * FROM users WHERE email = ?", [$email]);
```

### 4. XSS 与 CSRF 防御

- **Inertia.js**: 默认对所有数据进行转义。
- **CSRF**: Laravel 默认启用 Web 中间件的 CSRF 保护。
- **Header**: 配置 `Content-Security-Policy` 响应头。

### 5. 文件上传安全性

```php
// ✅ 校验文件
$request->file('avatar')->store('avatars', 'public');
$request->validate([
    'avatar' => 'image|max:1024', // 限 1MB，限图片
]);
```

## 常见漏洞防御

| 漏洞 | Laravel 防御方法 |
|------|-----------------|
| SQLi | Eloquent / 参数绑定 |
| XSS | Blade/Inertia 自动转义 |
| CSRF | `@csrf` 指令 / 中间件 |
| Mass Assignment | `$fillable` / `$guarded` |
| IDOR | Policy 授权校验 |

## 速率限制 (Rate Limiting)

```php
// routes/web.php
Route::middleware('throttle:60,1')->group(function () {
    Route::post('/api/search', [SearchController::class, 'index']);
});
```

## 部署前强制检查记录

- [ ] `.env` 已正确配置且不包含硬编码泄露
- [ ] 所有控制器方法均有 Policy 授权校验
- [ ] 所有用户输入均经过 FormRequest 验证
- [ ] 生产环境 `APP_DEBUG` 已关闭 (false)
- [ ] `APP_KEY` 已生成
- [ ] `npm audit` 和 `composer audit` 运行通过

---

**谨记**: 安全不是一个功能，而是一个过程。Laravel 虽然提供了许多内置保护，但正确的实施仍然依赖于开发者的警惕性。
