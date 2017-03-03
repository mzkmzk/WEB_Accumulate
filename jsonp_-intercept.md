# jsonp拦截

# 背景

在做一个Mock api的小插件,xhr通过重写实现了

但是JSONP怎么Mock JSONP就是

1. 定义好callback函数
2. 发出script标签然后返回的是callback(数据)的形式

# 事件捕抓

//只会经过document 不会经过window
```javascript
    document.addEventListener('load',function(e){

      console.log(e);

    },true);
```

## 顺序

是先执行callback()函数,还是先被捕抓到,

实验证明是先执行callback()函数,后被捕抓到

这样 看起来 我们貌似没办法