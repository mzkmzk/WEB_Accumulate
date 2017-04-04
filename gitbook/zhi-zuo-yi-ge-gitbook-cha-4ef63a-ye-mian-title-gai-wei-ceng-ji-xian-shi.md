# 制作一个gitbook插件: 页面title改为层级显示

# 背景

默认gitbook生成的title是 `本页面的标题 · 书名`

然而很奇怪 

例如一级目录是JS 其下一个page的标题为入门 书名叫404k的前后端日志

这样生成html的标题就为 入门 · 404k的前后端日志

而这样对SEO可能并不友好

我觉得 入门 · JS · 404K的前后端日志

这样可能会好点 

然后就撸起袖子

# 学习gitbook插件

首先建议可以先看看

`https://toolchain.gitbook.com/plugins/create.html` 了解其gitbook提供的钩子和API

