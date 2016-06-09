# Closure

# 1. 应用场景

1. 由使用者执行的一些逻辑
2. 由使用者决定一个boolean
3. 由使用者改变一个物件

# 2. 由使用者执行的一些逻辑

Laravel中的migrate中,创建表时,就大量使用了Closure

```php
Schema::create('Institutes', function (Blueprint $table) {
    $table->string('code');
    $table->string('address');
    $table->string('contact_person');
    $table->string('contact_phone');
    $table->string('comment');
});
```

对应的Laravel源码

`Illuminate/Database/Schena/Builder.php`第126行
```php
public function create($table, Closure $callback)
{
    $blueprint = $this->createBlueprint($table);
    $blueprint->create();
    $callback($blueprint);
    $this->build($blueprint);
}
```

## 3. 由使用者决定一个boolean

例如Laravel的`collection->firtst()`

使用

```php
collect([1, 2, 3, 4])->first(function ($key, $value) {
    return $value > 2;
});
```

Laravel实现源码

```php

```

# 参考资料

<http://oomusou.io/php/php-closure-practice/>