# 计算网页网络性能

# 环境

1. phantomjs: 2.1.1

# 目标

输入url,可以计算得出

1. DOMContentLoaded回调时间
2. 白屏时间
3. 首屏时间
4. window.onload时间
5. 资源请求数量和大小
6. 加载中的图片比较

# 思路

1. 给page嵌入监听DOMContentLoaded和window.onload
2. 加载页面,不断对页面进行page.renderBase64和page.render
3. 通过page.onLoadStarted、page.onResourceRequested和page.onResourceReceived统计资源个数和大小
4. 页面加载完了之后,把page.renderBase64的图片放进canvas,进行感知哈希算法进行比较相似度

# 计算DOMContentLoaded和window.onload

这里主要是injectJs进去,监听DOMContentLoaded,然后在page加载完成后,取这两个值出来

phantomjs
```javascript
page.onInitialized = function() {
    page.injectJs('../JS/get_data.js')
}

page.open(address, function (status) {
    var k_report = page.evaluate(function(){
            return window.k_report;
        })
}
```

../JS/get_data.js
```javascript
window.k_report = {
    dom_complete: null,
    window_onload: null
}

document.addEventListener( 'DOMContentLoaded', function(){
    window.k_report.dom_complete = new Date().getTime();
})

window.onload = function(){
    window.k_report.window_onload = new Date().getTime();
}

```

# 白屏时间、首屏时间、加载中的图片比较

## 截图

定义屏幕宽高为 1400x900 `page.viewportSize = { width: 1400, height: 700 }`

由于考虑到截图的效率,默认截图是截全网页的,而如果每次截这么大,截图花费时间比较多,所以只截取中间的一部分

```javascript
page.clipRect = {
    top: 400,
    left: 200,
    width: 600,
    height: 300
};
```

设置开始截图时机和关闭截图时机

```javascript
this.page.on_loadstarted_promise.push(function(){
    self.get_interval_capture();
})

this.page.on_loadfinished_promise.push(function()
    return  new Promise(function(resolve){
        clearInterval( self.intervalId );
           //setTimeout(function(){
           page.evaluate(function(screenshot){              
                window.copy_to_html(screenshot);
                window.judge_picture_evaluate(screenshot);
           },screenshot);

            self.page.on_callback_promise.push(function(data){
                if (data && data.command === 'computer_similar_exit') {
                    resolve();
                }
            })            
    })
       
})    


```



## 遗留问题

还有待解决的是图片的数量

因为服务器进行截图是需要耗时间的,如果截图越少,就越不准确



# 比较图片相似度: 感知哈希算法

1. 将图片缩放到canvas上,大小为8x8px
2. 将图片转为灰度图 ,转换算法为`Y =0.299 * R + 0.587 * G + 0.114 * B`
3. 计算灰度图的hash值: 将每个像素的灰度，与平均值进行比较。大于或等于平均值，记为1；小于平均值，记为0。然后转为16进制(结果为16位的16进制) 
4. 计算两张图片的汉明距离: 两张图的hash值,进行位置间的对比.
5. 计算结果: 如果不相同的数据位不超过5，就说明两张图片很相似；如果大于10，就说明这是两张不同的图片

实现算法文件: 主要是玩弄下canvas: https://github.com/mzkmzk/K-Reptile/blob/master/Src/JS/similar_picture.js

# 参考链接 

1. 感知哈希算法的介绍: http://www.ruanyifeng.com/blog/2011/07/principle_of_similar_image_search.html
2. 感知哈希算法的js实现: http://zhangmhao.github.io/2015/11/16/find-similar-picture/


