# Generator

# 1. 语法

```php
function myGenerator() 
{
    yield 'value1';
    yield 'value2';
    yield 'value3';
}

foreach (myGenerator() as $yieldedValue) {
    echo $yieldedValue, PHP_EOL;
}

//结果
value1
value2
value3
```

# 2. 使用环境

1. 相当于多个return,