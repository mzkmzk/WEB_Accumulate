# 前端安全知识

# XSS

### 攻

### 检

1. XSS 很多是通过内联事件来触发的
2. 窃听到的数据 或者 加载的黑客JS脚本 都是需要发网络请求的

第二点中

### 防

# CSRF

### 攻

### 检

### 防

# 劫持

### 攻

### 检

### 防

鸡肋的标签检查: https://div.io/topic/443

# 坏人们到底想干嘛?

无论是不良黑客 或者 运营商 他们到底要干嘛?

其实如果不是利益驱动 他们到底为什么要搞我们的网站

从根本上来说 他们的目的是这两个

1. 窃听数据
2. 做广告
3. 伪装成用户

窃听数据的 检查方案就是

window 捕抓 load 和 error 事件,并存储到一个数组A

等待window.onload时 要么获取

1. performance.getEntries() 20180810测试 只有chrome和firefox 支持该属性, safari 不支持, 但捕抓到的资源非常准确
2. 取之前数组A 获取到其URL 捕抓不到xhr和img.src(未插入到DOM)的发包 

大致的实现代码是

```javascript
window.addEventListener("load",  function(event){
     window._windowRequesLoadtArray.push(event)
}, true);

window.addEventListener("error",  function(event){
     window._windowRequesLoadtArray.push(event)
}, true);
```

```javascript
export default class CheckRequestHost {
    constructor(options, errorObj){
        this.options = options.checkRequestHost
        // document.readyState === ''
        if( this.options ){
            this.writeArray = [/xunlei\.com$/, 
                                /a\.88cdn\.com/, 
                                /baidu\.com$/,
                                /kanimg\.com$/,
                              ].concat( this.options.writeArray || [])
            //this.lastPerformanceEntriesIndex = 0
            this.errorObj = errorObj
        }
    }

    onLoad(){
        let callback = () => {
            
            if ( performance && performance.getEntries ){
                this.checkUrl( performance.getEntries(), 'name' )
            }else {
                let arr = [],
                    element, url
                for (var i = 0; i < window[ CONSTANT.REQUEST_ARRAY ].length; i++) {
                    element = window[ CONSTANT.REQUEST_ARRAY ][i]

                    if ( element && 
                        element.path && 
                        element.path[0] &&
                        url = (element.path[0].src || element.path[0].href )){
                        arr.push( { url: url} )
                    }
                }
                this.checkUrl( window[ '_windowRequesLoadtArray' ] || [] , 'url' )
            }
        }

        if (  document.readyState === 'complete' ){
            callback()
        }else {
            window.addEventListener( 'load', callback )
        }
    }

    checkUrl(urlArray, urlAttributeName){
        let reportUrlArray = [],
            matchHost, host, i, j
        for ( i = 0; i < urlArray.length; i++) {
            matchHost = urlArray[i][urlAttributeName].match(/^https?:\/\/(.*?)(?:[\/?&]|$){1}?/)
            if ( !matchHost || matchHost.length < 1 ){
                continue
            }
            host = matchHost[1]
            for ( j = this.writeArray.length - 1; j >= 0; j--) {
                if ( this.writeArray[j].test( host  ) ) {
                    continue
                }
            }
            reportUrlArray.push( host )
        }
        this.errorObj.push({
             isCustom: true,
             eventType: 11001, //自定义错误ID 建议2W以上
             message: reportUrlArray.join(' ')
        })
        return reportUrlArray
    }
}
```

而做广告的话

它要么 进入页面前 就跳走了 这个H5本身没任何办法..如果H5嵌入自家APP的话 看自己APP能否帮忙

要么通过发广告包(通过上面监听网络可捕抓)

要么嵌入iframe (通过上面监听网络可捕抓) 



# 参考链接

1. 运营商劫持: https://js8.in/2017/08/04/Web%E5%89%8D%E7%AB%AF%E9%A1%B5%E9%9D%A2%E5%8A%AB%E6%8C%81%E5%92%8C%E5%8F%8D%E5%8A%AB%E6%8C%81/
2. 安全系列文章: https://div.io/user/118