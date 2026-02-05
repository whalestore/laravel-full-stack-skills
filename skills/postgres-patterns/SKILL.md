---
name: postgres-patterns
description: PostgreSQL 数据库模式，涵盖查询优化、Schema 设计、索引和 Laravel Eloquent 集成。
---

# PostgreSQL Patterns (Laravel Optimized)

PostgreSQL 最佳实践手册，特别针对 Laravel Eloquent ORM 进行优化。

## Laravel 与 Postgres 集成

### 1. 数据库迁移 (Migrations)

```php
// ✅ 推荐：使用 JSONB 存储半结构化数据
$table->jsonb('meta_data')->default('{}');

// ✅ 推荐：为经常查询的字段添加索引
$table->index(['user_id', 'created_at']);
```

### 2. Eloquent 性能优化

```php
// ❌ 避免: N+1 问题
$orders = Order::all();
foreach ($orders as $order) {
    echo $order->user->name;
}

// ✅ 推荐: 预加载
$orders = Order::with('user')->get();
```

## 核心模式

### 索引速查表

| 查询模式 | 索引类型 | 示例 |
|---------|---------|------|
| `WHERE col = val` | B-tree | `INDEX (col)` |
| `WHERE jsonb @> '{}'` | GIN | `INDEX USING gin (col)` |
| `WHERE col LIKE '%val%'` | GIN (trgm) | `INDEX USING gin (col gin_trgm_ops)` |
| 时间范围查询 | BRIN | `INDEX USING brin (col)` |

### 常用 SQL 模式

**复合索引顺序:**
- 等值判断字段在前，范围查询字段在后。
- `CREATE INDEX idx_status_created ON orders (status, created_at);`

**UPSERT (Laravel 原生支持):**
```php
$user->updateOrCreate(
    ['email' => 'test@example.com'],
    ['name' => 'Test User']
);
```

**游标分页 (适合大数据量):**
```php
$users = User::orderBy('id')->cursorPaginate(15);
```

## 性能分析

### 1. 分析慢查询

```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 1;
```

### 2. 检查未被引用的外键索引

```sql
-- 查找没有索引的外键
SELECT conrelid::regclass, a.attname
FROM pg_constraint c
JOIN pg_attribute a ON a.attrelid = c.conrelid AND a.attnum = ANY(c.conkey)
WHERE c.contype = 'f'
  AND NOT EXISTS (
    SELECT 1 FROM pg_index i
    WHERE i.indrelid = c.conrelid AND a.attnum = ANY(i.indkey)
  );
```

## 数据库设计准则

- **UUID**: 适合分布式，但注意 B-tree 性能。
- **JSONB**: 除非必要，否则优先使用结构化列。
- **Timestamps**: 始终包含 `created_at` 和 `updated_at`。
- **Soft Deletes**: 大型表考虑索引 `deleted_at` 字段。

---

*针对 Laravel 12.x 和 PostgreSQL 16+ 优化。*
