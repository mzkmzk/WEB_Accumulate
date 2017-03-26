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
    self.intervalId = setInterval(function(){
        self.capture_array.push( self.page.renderBase64('PNG') ) 
    },0)
})

this.page.on_loadfinished_promise.push(function()
    return  new Promise(function(resolve){
        clearInterval( self.intervalId );
        page.evaluate(function(screenshot){              
            window.judge_picture_evaluate(screenshot); //js计算相似度
        },screenshot);

        self.page.on_callback_promise.push(function(data){
            if (data && data.command === 'computer_similar_exit') { //等待计算完毕
                resolve();
            }
        })            
    })
       
})    
```

## 计算白屏和首屏时间

当白屏的时候,图片经过计算的hash码是'100000000000000000000000000000000'的

而拿最后一张图,和之前的图进行对比,最先和最后一张图类似的,即为首屏时间

```javascript
for (var i = 0; i < _atf.capture_array.length ; i++) {

    try {
        
        var result = similar_result( _atf.capture_array[i].image, _atf.capture_array[ _atf.capture_array.length - 1 ].image )

        if ( window.similar_picture.white_screen === -1 && result.hash_1 !== '100000000000000000000000000000000' ) {
            window.similar_picture.white_screen = _atf.capture_array[i].time;
        }
        if ( window.similar_picture.atf === -1 && result.hash_1 === result.hash_2 ) {
            window.similar_picture.atf = _atf.capture_array[i].time;
        }    
    }catch(e){
        alert(e)
    }

}
```

## 遗留问题

还有待解决的是图片的数量

因为服务器进行截图是需要耗时间的,如果截图越少,就越不准确

## window.callPhantom 

只能使用一次 js里定义两次,发现两个都没被触发

# 比较图片相似度: 感知哈希算法

1. 将图片缩放到canvas上,大小为8x8px
2. 将图片转为灰度图 ,转换算法为`Y =0.299 * R + 0.587 * G + 0.114 * B`
3. 计算灰度图的hash值: 将每个像素的灰度，与平均值进行比较。大于或等于平均值，记为1；小于平均值，记为0。然后转为16进制(结果为16位的16进制) 
4. 计算两张图片的汉明距离: 两张图的hash值,进行位置间的对比.
5. 计算结果: 如果不相同的数据位不超过5，就说明两张图片很相似；如果大于10，就说明这是两张不同的图片

实现算法文件: 主要是玩弄下canvas: https://github.com/mzkmzk/K-Reptile/blob/master/Src/JS/similar_picture.js

实现代码

```javascript
function resize2Canvas(img, width, height) {
    if (!img || !width) {
        return img;
    }
    height = height || width;
    // 按原图缩放
    var detImg = img.width / img.height;
    if (width / height > detImg) {
        height = width / detImg;
    } else {
        width = height * detImg;
    }
    // 画到 canvas 中
    var canvas = document.createElement('canvas');
    canvas.width = width;
    canvas.height = height;
    var ctx = canvas.getContext('2d');
    ctx.drawImage(img, 0, 0, width, height);
    return canvas;
}

/**
 * 将canvas处理成灰度图
 */
function grayscaleCanvas(canvas) {
    var canvasContext = canvas.getContext('2d');
    var cWidth = canvas.width;
    var cHeight = canvas.height;
    var canvasData = canvasContext.getImageData(0, 0, cWidth, cHeight);
    var canvasDataWidth = canvasData.width;

    for (var x = 0; x < cWidth; x++) {
        for (var y = 0; y < cHeight; y++) {

            // Index of the pixel in the array
            var idx = (x + y * canvasDataWidth) * 4;
            var r = canvasData.data[idx + 0];
            var g = canvasData.data[idx + 1];
            var b = canvasData.data[idx + 2];

            // calculate gray scale value
            var gray = Math.ceil((0.299 * r + 0.587 * g + 0.114 * b) / 4);


            // assign gray scale value
            canvasData.data[idx + 0] = gray; // Red channel
            canvasData.data[idx + 1] = gray; // Green channel
            canvasData.data[idx + 2] = gray; // Blue channel
            canvasData.data[idx + 3] = 255; // Alpha channel
        }
    }
    canvasContext.putImageData(canvasData, 0, 0);
    return canvasData;
}

/**
 * 将16进制的图片hash转化为二进制
 */
function hashToBinaryArray(h) {
    return parseInt(h, 16).toString(2);
}

/**
 * 计算图片的hash值
 */
function hash(img) {
    var size = 8;
    var resizedCanvas = resize2Canvas(img, size, size, false);
    var canvasData = grayscaleCanvas(resizedCanvas);
    var cW = canvasData.width,
        cH = canvasData.height;
    var totalGray = 0,
        x, y, idx, grayValue;
    for (x = 0; x < cW; x++) {
        for (y = 0; y < cH; y++) {
            // Index of the pixel in the array
            idx = (x + y * cW) * 4;
            grayValue = canvasData.data[idx];
            totalGray += grayValue;
        }
    }
    var meanGray = totalGray / (size * size);
    var val;
    var array = [];
    for (x = 0; x < cW; x++) {
        for (y = 0; y < cH; y++) {
            // Index of the pixel in the array
            idx = (x + y * cW) * 4;
            grayValue = canvasData.data[idx];
            if (grayValue >= meanGray) {
                val = 1;
            } else {
                val = 0;
            }
            array.push(val);
        }
    }
    return parseInt(array.join(''), 2).toString(16);
}

function hamming(h1, h2) {
    var h1a = hashToBinaryArray(h1);
    var h2a = hashToBinaryArray(h2);
    var diff = 0;
    for (var i = 0; i < h1a.length; i++) {
        diff += h1a[i] ^ h2a[i];
    }
    return diff;
}

function similar_result(img1, img2) {
    var hash_1 = hash(img1);
    var hash_2 = hash(img2);
    var threshHold = 10;
    var hamming_diff = hamming(hash_1, hash_2);

    return {
        hamming_diff: hamming_diff,
        hash_1: hash_1,
        hash_2: hash_2
    }
}

```

# 参考链接 

1. 感知哈希算法的介绍: http://www.ruanyifeng.com/blog/2011/07/principle_of_similar_image_search.html
2. 感知哈希算法的js实现: http://zhangmhao.github.io/2015/11/16/find-similar-picture/


