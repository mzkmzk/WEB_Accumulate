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

微信没唤起支付宝

## 