# Closure

# 1. 应用场景

1. 由使用者执行的一些逻辑
2. 由使用者决定一个boolean
3. 由使用者改变一个物件

其实核心的逻辑都是: 我在我的函数为你写好上下文,中间一些具体的不同的地方,你通过闭包来改变.

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

# 3. 由使用者决定一个boolean

例如Laravel的`collection->firtst()`

使用

```php
collect([1, 2, 3, 4])->first(function ($key, $value) {
    return $value > 2;
});
```

Laravel实现源码

`Illuminate/Support/Arr.php`
```php
public static function first($array, callable $callback = null, $default = null)
    {
        if (is_null($callback)) {
            return empty($array) ? value($default) : reset($array);
        }

        foreach ($array as $key => $value) {
            if (call_user_func($callback, $key, $value)) {
                return $value;
            }
        }

        return value($default);
    }
```


# 4. 由使用者改变一个物件 

# 参考资料

<http://oomusou.io/php/php-closure-practice/>