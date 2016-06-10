# Trait

php 5.4以上职称

# 1. 简介

trait是介于extend 和 interface 的一种实现方式

1. exntend: 强制子类继承父类的所有可继承的方法和属性
2. interface: 强制子接口去实现父接口的所有方法
3. trait: 把公用的方法抽取出来定义为`trait a {...}`类,只需要在用到这些公用的方法的类b中写 `use a;`(注意实在class里写), b即可用到a中所有定义的方法(包含private,protected,publis)

简言而之: use trait类,就跟把其代码复制过来,一毛一样

# 2. 冲突

如果有trait a 和trait b 实现了同一个方法,那么这样use肯定会出现命名冲突啊,咋办

```php
trait A 
{
    function someFunc() 
    {
        ...
    }

    function otherFunc() 
    {
        ...
    }
}

trait B 
{
    function someFunc() 
    {
        ...
    }

    function otherFunc() 
    {
        ...
    }
}

class MyClass 
{
    use A, B {
        A::someFunc insteadof B;
        B::otherFunc as differentFunc;
    }
}
```

1. `A::someFunc insteadof B`: 代表用a的someFunc,而不用B的
2. `B::otherFunc as differentFunc`: 代表B的otherFunc改名为differentFunc
3. 这样写的话,B中的someFunc,是永远无法被调用的
4. 还是建议一个class只引用一个trait的

# 3. 选择继承 多态 Trait

1. 继承: 想要重复使用的既有程序功能
2. 多态: 想要解耦合既有程序功能
3. Trait: 想要共享相同的程序功能




