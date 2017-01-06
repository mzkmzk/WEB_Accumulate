# 读取package.json

# 原因

需要在webpack.config.js获取package.json的name和version

# 解决

一开始比较疑惑 package.json里没有export,如果直接require进来,应该是不行的...

但是原来是可以的.....

```javascript
var package = require("./package.json");

var name = package.name;
var version = package.version;
```

# 参考链接

1. https://cnodejs.org/topic/50ec24775daa681f0a3487e6