# 前端安全知识

# XSS

### 攻

反射型XSS: 在url中添加参数, 假如JS里取了这个参数 然后直接填充到HTML中, 就容易引发反射型XSS

存储型XSS: 例如在论坛里的评论区 黑客A评论了有害的脚本内容 有害内容存储到了服务器中 用户B在加载到黑客A的评论的时 就触发了XSS  

更多攻的思路可参考: https://wps2015.org/drops/drops/Bypass%20xss%E8%BF%87%E6%BB%A4%E7%9A%84%E6%B5%8B%E8%AF%95%E6%96%B9%E6%B3%95.html

### 检

XSS攻击很多都是在html上面去做手脚

特别是在内联事件中

比如

```html
<img src="{路径}" />

<img src="{路径" onload="alert(/xss/)}" />
```

所以检测内联事件的代码是一种思路 

```javascript
;(function(){
    function hookEvent(onevent) {
        document.addEventListener(onevent.substr(2), function (e) {
            var element = e.target;

            // 跳过已扫描的事件
            var flags = element['_flag'];
            if (!flags) {
                flags = element['_flag'] = {};
            }
            if (typeof flags[onevent] != 'undefined') {
                return;
            }
            flags[onevent] = true;

            if (element.nodeType != Node.ELEMENT_NODE) {
                return;
            }
            var code = element.getAttribute(onevent);
            if (code && /xss|alert|eval|setTimeout|setInterval|Function/.test(code)) {
                // element[onevent] = null; //拦截内联事件
                window._windowVipReportErrorArray.push({
                    isCustom: true,
                    eventType: 11002, //自定义错误ID 建议2W以上
                    message: code
                 })
            }
        }, true);
    }

    if ( document.addEventListener ){
        for (var k in document) {
            if (/^on/.test(k)) {
                hookEvent(k);
            }
        }
    }
    
})()
```

这段代码主要是

1. 遍历document的on属性进行绑定事件
2. 对绑定过一次的事情 以后直接跳过

### 防

防XSS 最重要的还是靠读取URL参数、读取用户输入数据和获取后端数据的时候

必须要过滤或转义

对用户输入来说HTML编码

|特舒符|实体编码|
|---|---|
| & | `&amp;` |
| < | `&lt;` |
| > | `&gt;`|
| " | `&quot;` |
| ' | `&#x27;` |
| / | `&#x2F;` |

简单的过滤下

```javascript

function escapeHtml(value) {
  if (typeof value !== 'string') {
    return value
  }
  return value.replace(/[&<>`"'\/]/g, function(result) {
    return {
      '&': '&amp;',
      '<': '&lt;',
      '>': '&gt;',
      '`': '&#x60;',
      '"': '&quot;',
      "'": '&#x27;',
      '/': '&#x2f;',
    }[result]
  })
}
```

但是通过上面的转义 并不要以为就这样就结束了 黑客们其他怪招还很多...

还一颗通过CSP来防止内联脚本的执行

```html
<meta http-equiv="Content-Security-Policy" content="script-src *.baidu.com baidu.com ... 'unsafe-inline' 'unsafe-eval';">
```

或者通过一个脚本来帮助标签和属性的白名单设置`https://jsxss.com/zh/index.html`


# CSRF

### 攻

类似攻击的是在a.com里有一个接口c 是改变收货地址的

在hacker.com 发 c接口 结果c接口还执行成功了

导致hacker.com的所有者 可以随便模仿用户 改变收货地址

### 检

检查的话 接口方检查检查referfrom 非白名单的话就记录下

### 防

1. token每次刷新页面 会生成一个token 在input hidden的value里, 每次发请求前取出来给到服务器
2. 服务器验证白名单referfrom
3. 验证码
4. 设置验证请求头: 例如取出cookie里的userid sessionid 加上对称密钥和时间戳 然后base64 发到服务器验证

# 劫持

### 攻

运营商或者DNS解析 串改JS HTML 的内容

### 检

其实比较简单的校验方法有 

 HTML 文件里 写好MD5值

如果发现执行时 HTML内容和写好的MD5值不一致 上报HTML内容

但是这个方案不好实施是 每次改变HTML内容的时 都要重新写MD5值 是比较麻烦的

### 防

可以加CSP规则 阻止其他域的script和iframe加载

终极大招 HTTPS+HSTS

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

而窃听数据时 一般都会发包的攻击者的服务器上的 

而伪装成用户的话 就靠上面说的CSRF防范

# 参考链接

1. 运营商劫持: https://js8.in/2017/08/04/Web%E5%89%8D%E7%AB%AF%E9%A1%B5%E9%9D%A2%E5%8A%AB%E6%8C%81%E5%92%8C%E5%8F%8D%E5%8A%AB%E6%8C%81/
2. 安全系列文章: https://div.io/user/118
3. etherdream的博客: https://www.cnblogs.com/index-html/
4. etherdream的github: https://github.com/EtherDream
5. 基础型的XSS说明: https://mp.weixin.qq.com/s/6ChuUdOm7vej8vQ3dbC8fw