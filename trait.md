# Trait

php 5.4以上职称

# 1. 简介

trait是介于extend 和 interface 的一种实现方式

1. exntend: 强制子类继承父类的所有可继承的方法和属性
2. interface: 强制子接口去实现父接口的所有方法
3. trait: 把公用的方法抽取出来定义为`trait a {...}`类,只需要在用到这些公用的方法的类b中写 `use a;`, b即可用到a中所有定义的方法(包含private,protected,publis)

