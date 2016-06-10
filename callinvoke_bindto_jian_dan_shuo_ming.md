
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

# 4. 读取类中原本属性

之前的helloworld当中,没用到类中定义到的变量

例如

```javascript
function Foo(name) {
    this.name = name;
}

var obj = new Foo('Sam');
obj.say = function () {
    return "Hello " + this.name;
};
print(obj.say());

```

php如何捕抓到类中的name

直接`$this->name`,这样写的话$this只会指向到say函数

# 5. bindTo()

```php
class Foo
{
    private $name;

    function __construct($name)
    {
        $this->name = $name;
    }

}

$obj = new Foo('Sam');

$cl = function() {
    return "Hello " . $this->name;
};

$cl = $cl->bindTo($obj, $obj);
echo($cl());

// Result:
// Hello Sam
```

bingTo方法

1. 若只要public变量,只传第一个参数就可以
2. 参数可以是变量/class名称等.

