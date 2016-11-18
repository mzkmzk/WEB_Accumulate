# 绑定事件

react绑定事件,其实和普通DOM差不多.

不过需要注意的是

如果是DOM2也就是addEventListener绑定的话 记得在compoentWillUnmount取消掉绑定,因为每个组件都可能渲染很多次的


