# 防止页面劫持探究

# 背景

产品发来一张截图, 是我们的支付页面里被嵌入了一个竞品的广告

产品大发雷霆, 说无论如何也要尽快把这些广告干掉

然而最稳的方法就是切https啊...但是这在已经发展了几年的域名网站里...

这可是一项大工程哇.....

所以只能另辟蹊径...

# 劫持方式

1. 嵌入iframe(以赚广告量)
2. 污染源JS，修改为加载广告埋点和加载源JS

# Content Security Policy

这个是现代浏览器中最稳的方式了

语法: 一个指令的多个值用英文空格分开, 多个指令间用

## 指令

| 指令        | 说明                                                                                                                                        |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| default-src | 定义针对所有类型（js、image、css、web font，ajax 请求，iframe，多媒体等）资源的默认加载策略，某类型资源如果没有单独定义策略，就使用默认的。 |
| script-src  | 定义针对 JavaScript 的加载策略。                                                                                                            |
| style-src   | 定义针对样式的加载策略。                                                                                                                    |
| img-src     | 定义针对图片的加载策略。                                                                                                                    |
| connect-src | 针对 Ajax、WebSocket 等请求的加载策略。不允许的情况下，浏览器会模拟一个状态为 400 的响应。                                                  |
| font-src    | 针对 WebFont 的加载策略。                                                                                                                   |
| object-src  | 针对 、 或,等标签引入的 flash 等插件的加载策略。                                                                                            |
| media-src   | 针对,或,等标签引入的 HTML 多媒体的加载策略。                                                                                                |
| frame-src   | 针对 frame 的加载策略。                                                                                                                     |
| sandbox     | 对请求的资源启用 sandbox(类似于 iframe 的 sandbox 属性)[https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe]）。                                                                                 |
| report-uri  | 告诉浏览器如果请求的资源不被策略允许时，往哪个地址提交日志信息，只提交日志，不阻止任何内容(仅对Content-Security-Policy-Report-Only头有效)。 |

指令值

| 指令值          | 说明                                                                                   |
|-----------------|----------------------------------------------------------------------------------------|
|                 | 允许任何内容(默认)                                                                     |
| 'none'          | 不允许任何内容                                                                         |
| 'self'          | 允许同域内容                                                                           |
| data:           | 允许data: 的图片                                                                       |
| www.a.com       | 允许加载指定域名的资源。                                                               |
| .a.com          | 允许加载 a.com 任何子域的资源。                                                        |
| https://a.com   | 允许加载 a.com 的 https 资源（协议需匹配）。                                           |
| https:          | 允许加载 https 资源。                                                                  |
| 'unsafe-inline' | 允许加载 inline 资源（例如常见的 style 属性，onclick，inline js 和 inline css 等等）。 |
| 'unsafe-eval'   | 允许加载动态 js 代码，例如 eval()。                                                    |

例子: 假如我只能加载xunlei和 baidu和sina的js

`<meta http-equiv="Content-Security-Policy" content="script-src *.xunlei.com xunlei.com *.baidu.com baidu.com *.sina.com.cn 'unsafe-inline' 'unsafe-eval';">`

假如最后`'unsafe-inline' 'unsafe-eval'`不加的话 则html里内嵌的js无法执行, 并且类似evel等动态执行代码都无法执行

需要注意的是

1. 假如想可以匹配a.com和a.com的子域名, 需要分开写,`a.com *.a.com`
2. IE兼容性为10及其以上(而且IE10, IE11 要设置为`X-Content-Security-Policy`)

对于`unsafe-inline` 会允许内联代码

但是有时劫持会直接在html加script

而我们页面本身又是需要内联script的, 该如何处理呢

```html
Content-Security-Policy: script-src 'nonce-EDNnf03nceIOfn39fn3e9h3sdfa'

<script nonce=EDNnf03nceIOfn39fn3e9h3sdfa>
  //Some inline code I cant remove yet, but need to asap.
</script>
```

用户就是nonce-后面跟随机数, 但是这个用户在IE内都是不支持的, 所以暂时还是保留`unsafe-inline`

值得注意的是跟`Content-Security-Policy`类似的是`Content-Security-Policy-Report-Only`

语法基本一致, 但是后者不会阻拦任何内容, 而只是向`report-uri`指令设置的地址发送检测到异常的报告

## 具体方案

DNS劫持方的劫持方式一般有

1. 嵌入iframe
2. 通过污染js源文件，然后加载劫持方的js

`<meta http-equiv="Content-Security-Policy" content="https: 'unsafe-inline' 'unsafe-eval';">`



script-src

# 参考链接

1. CSP2的w3文档 https://www.w3.org/TR/CSP2/
2. 指令详解: https://imququ.com/post/content-security-policy-reference.html
3. 一些安全相关的HTTP响应头: https://imququ.com/post/web-security-and-response-header.html
4. 检查被劫持情况: http://www.cnblogs.com/kenkofox/p/4919668.html
5. 谷歌相关说明: https://developers.google.com/web/fundamentals/security/csp/?hl=zh-cn
6. 同源策略的限制: https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy
