# 第一次发布自己的npm插件

额需要注意的是如果你的npm不是正规的npm源的话,是不行滴

```shell
npm config get registry
https://registry.npmjs.org/
```

1.  npm adduser|login,填写username,email,password
2.  npm publish 即可,记得先填写好package.json