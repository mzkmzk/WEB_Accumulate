# 打造常用易用的javascript库

# 背景

当我们每次在写业务代码时,经常会操作获取url参数,判断是什么浏览器,操作cookie等一些常用的操作,每次我们都需要去找之前写的代码,然后复制粘贴过来使用,着实麻烦.

# 愿景

我们常用的操作,只需npm install一个库,然后import不同部分的组件

# 问题分析和解决

## 按需加载

例如我们只需要操作cookie的库

```
import Cookie from 'K_Utils/Cookie'
```

即可,这样只会引入和Cookie相关的库

我们拒绝引入一小部分就来一大坨代码的库

这里主要参考到了`https://github.com/callemall/material-ui`

