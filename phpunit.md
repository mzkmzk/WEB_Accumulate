# PHPUnit

参考链接<https://phpunit.de/manual/4.8/zh_cn/writing-tests-for-phpunit.html>

##1. 基本测试用法

###1.1 依赖

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

`@depends`指明依赖关系,