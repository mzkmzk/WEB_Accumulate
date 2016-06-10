# Closure

# 1. 应用场景

1. 由使用者执行的一些逻辑
2. 由使用者决定一个boolean
3. 由使用者改变一个物件

其实核心的逻辑都是: 

1. 我把通用的模块抽取出来,并提供一个Closure参数
2. 使用者通过这个Closure来执行各自特定的行为

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

## 2.1 实践

在前端php,调用后端Go接口时,需要做一些重复的校验

```php
$rs = 调用后端接口. 
if($rs)
    if($rs->err_code==0){
      ...
        return response()->json(array('rs'=>'success',...);
    }else if ($rs->err_code == 9999){
        return response()->json(array('rs'=>'error',...);
    }else{
        return response()->json(array('rs'=>'error','msg'=>$rs->err_msg));
    }
}else{
    return response()->json(array("rs"=>"error","msg"=>$this->sys_model->get_last_err_msg()));
}
}
```

这里只有针对特定错误码时的代码有用

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

例如要实现
```sql 
SELECT * FROM posts WHERE (status = 0 or status = 1)
```

在Laravel中,需要这样

```php
Post::where(function ($query) {
    $query->where('status', 0)
           ->orWhere('status', 1);
})->get();
```

再看看Laravel源码

`Illuminate/Database/Eloquent/Build.php`
```php
class Build {
  public function where($column, $operator = null, $value = null, $boolean = 'and')
    {
        if ($column instanceof Closure) {
            $query = $this->model->newQueryWithoutScopes();

            call_user_func($column, $query);

            $this->query->addNestedWhereQuery($query->getQuery(), $boolean);
        } else {
            call_user_func_array([$this->query, 'where'], func_get_args());
        }

        return $this;
    }
}
```




# 参考资料

<http://oomusou.io/php/php-closure-practice/>