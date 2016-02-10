# PHPUnit

参考链接<https://phpunit.de/manual/4.8/zh_cn/writing-tests-for-phpunit.html>

##1. 基本测试用法

###1.1 依赖

```php
     /**
     * @depends 被依赖方法
     */
     public function testPush(array $stack)
     ...
```

`@depends`