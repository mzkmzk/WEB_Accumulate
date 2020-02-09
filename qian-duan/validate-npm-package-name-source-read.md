# validate-npm-package-name源码阅读

# 原理

- 检查传输package-name参数的类型, 是否为空, 是否为字符串等
- 检查有没有跟nodejs的一些原生库名重复
- 检查是否为无大写字母的包名
- 检查包名长度是否大于214

# 单元测试

是很好做的测试, 根据输入很直接就可以得到输出

# 依赖

- builtins: 只是列举nodejs原生库名, https://github.com/juliangruber/builtins#readme

# 意义

读这个包的意义, 其实是能规范一些我们自己起包名的规范