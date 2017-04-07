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

主要的结构为入口js 例如index.js

```javascript
module.exports = {
    // Map of hooks
    hooks: {},

    // Map of new blocks
    blocks: {},

    // Map of new filters
    filters: {}
};
```

一般主要用hooks,可以监听page生成前和生成时

block和filters主要是自定义数据模板的

# 修改最终的文件 

最后的思路是

利用page:before(page事件也行)中的page对象收集level和title

最终收集的数据类似

```javascript
    {
        "level": "1.7.1",
        "title": "只有偏执狂才能生存",
        "basename": "zhi_you_pian_zhi_kuang_cai_neng_sheng_cun"
    },
    {
        "level": "1.8",
        "title": "设计",
        "basename": "she_ji"
    },
    {
        "level": "1.8.1",
        "title": "至美用户人本设计剖析",
        "basename": "zhi_mei_yong_hu_ren_ben_she_ji_pou_xi"
    }
```




# 尝试过的失败

## 直接修改hooks中的page:before

page:before函数中有page.title属性,但是修改了并没什么用,title依旧

一般page.content修改就很好使

## hooks中的navigation修改title

```javascript
hooks: {
        "init": function(){
            
            for(key in this.navigation) {
                this.navigation[ key ].title = 12345;
            }
            console.log(circular_json.stringify(this, null, 4))

        },
```

然而这并没有修改到title


