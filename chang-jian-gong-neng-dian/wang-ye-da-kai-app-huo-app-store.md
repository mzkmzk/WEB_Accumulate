# 网页打开APP或APP_Store

# 尝试结果

## 微信和safari 均可打开APP STORE

```html
<a href="https://itunes.apple.com/cn/app/id333206289?mt=8">打开APPstore的 支付宝</a>
<a href="itms-apps://itunes.apple.com/cn/app/nuan-dao/id583307376?mt=8">打开APPstore的 暖岛</a>

```

## safari|微信 下打开支付宝(已安装)

```html
<a href="alipays://platformapi/startapp?appId=20000001&_t=1495616621235">跳转到支付宝</a>
```
safari能正常唤起支付宝

微信没唤起支付宝, 点击无反应 

## safari|微信 下打开xx(未安装)

```html
<a href="nuandao://web2app">打开APPstore的 nuandao</a>
```

safari显示: Safari打不开该网页, 因为网址无效

而微信依然是没反应 

# 参考链接

1. http://fegirl.com/2016/06/27/IOS9%20%E9%80%9A%E7%94%A8%E9%93%BE%E6%8E%A5%EF%BC%88universal%20link%EF%BC%89/