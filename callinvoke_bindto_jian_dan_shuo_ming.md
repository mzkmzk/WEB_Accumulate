# __call __invoke bindTo简单说明

# 1. javascript动态添加方法

```javascript
function Foo() {
}

var obj = new Foo();
obj.say = function () {
    return "Hello World";
};
print(obj.say());

// Result:
// Hello World
```

这种动态添加方法,php如何实现

# 2. __call

```php
class Foo
{
    public function __call($method, $args)
    {
        if(is_callable([$this, $method])) {
            return call_user_func_array($this->$method, $args);
        }
        // else throw exception
    }

}

$obj = new Foo('Sam');
$obj->say = function () {
    return 'Hello World';
};
echo($obj->say());

// Result:
// Hello World
```

# 3. __invoke

```php
class Foo
{
	
}

$obj = new Foo('Sam');
$obj->say = function () {
    return 'Hello World';
};
echo($obj->say->__invoke());

// Result:
// Hello World
```

# 4. 读取私人属性

