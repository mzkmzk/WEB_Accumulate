# PHPUnit

参考链接<https://phpunit.de/manual/4.8/zh_cn/writing-tests-for-phpunit.html>

#2. 基本测试用法

##2.1 依赖

```php
<?php
class MultipleDependenciesTest extends PHPUnit_Framework_TestCase
{
    public function testProducerFirst()
    {
        $this->assertTrue(true);
        return 'first';
    }

    public function testProducerSecond()
    {
        $this->assertTrue(true);
        return 'second';
    }

    /**
     * @depends testProducerFirst
     * @depends testProducerSecond
     */
    public function testConsumer()
    {
        $this->assertEquals(
            array('first', 'second'),
            func_get_args()
        );
    }
}
?>
```

`@depends`指明依赖关系,按依赖顺序在参数中返回.

## 2.2 数据提供器

数据提供器的要求

1. 必须为public
2. 返回值要么是
   1.一个数组
   2.每个元素也是数组
   3.实现Iterator接口的对象,迭代时每步产生一个数组

每个数组都会逐一被用来作为参数测试测试方法

同时使用依赖和数据提供器时,参数顺序:`数据供给器的参数将先于来自所依赖的测试`