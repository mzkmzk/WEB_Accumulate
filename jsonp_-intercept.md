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

这样 看起来 我们貌似没办法进行jsonp的mock

## jQuery

js执行顺序虽然这样

但是jquery是自己实现了一套类XHR系统

1. 将jsonp放入jquery的XHR系统中
2. window[callback]只是把接口返回的数据先存起来
3. 然后收到script的load事件才开始执行我们业务中写的success函数

所以如果我们这样进行事件捕抓

```javascript
    document.addEventListener('load',function(e){
      console.log(e);
    },true);
```

因为是事件捕抓,所以在script的load事件前,documen已经收到了load事件

此时在document中的load事件,再次执行一次window[callback]函数,但是参数不再是后台的数据,而是我们mock的数据

实现代码

```javascript
//match_array[1]为window中callback的函数名
  document.addEventListener('load',function(e){
      var match_array,
          item;
      if (e && 
            e.path && 
            e.path[0] &&  
            e.path[0].src && 
            (match_array = e.path[0].src.match(/callback=(\w+)/)) &&
            typeof match_array[1] === 'string' &&
            window[ match_array[1] ] &&
            ( item =   Utils.find_mocked_item({url: e.path[0].src}, XHR.Mock._mocked)  )
             ){
        window[ match_array[1] ].apply(null,[mock数据])
      }
      //console.log(e);
      //return false;
    },true);
```

缺陷还是有的

### Mock jquery 的error

现在我们可以获取到获取后台数据的script标签,但是怎么对script主动mock error

然后jquery捕抓到呢?

尝试了几种方法,例如

1. `$(e.path[0]).trigger('error')`
2. e.path[0].src="http://k-inner-report.404mzk.com/v1" 也无效

但是无效,应该是jquery不支持主动触发error

所以暂时放弃