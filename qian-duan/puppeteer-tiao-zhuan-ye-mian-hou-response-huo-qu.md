# puppeteer跳转页面后response获取

# 问题简述

业务场景 接受到了请求后 前端立马跳转了页面

```javascript
page.on('response', response => {
    if( response.url() === 目标接口)
        await response.json()
})
```

response.json是会报错的

因为页面跳转了. chromedevtools就查不到其响应内容

# 尝试方案

> 使用onbeforeunload拦截跳转

缺点: 页面没有正常的跳转

```javascript
await page.evaluate(() => {
  window.onbeforeunload = function(event) {
      return "stop navigating";
  };

});  
await page.on('dialog', (dialog)=>{
    dialog.dismiss()
})
```

原理就是通过onbeforeunload 拦截住页面

然后把弹窗取消掉 然后就页面没跳转就获取到数据了

但有没有方案 可以拿到数据 又可以跳转页面呢 

我之前的想法是 还是通过onbeforeunload拦截页面跳转

但是我同意跳转 就是监听到dialog事件后 

隔3S后再执行dialog.accept()

这样我们的response.json() 就可以获取到数据 

但是实际想 在dialog消失之前....response.json 也获取不到数据...

一定要dialog取消了 response.json 这个promise 才会返回回来 

所以这个即获取数据 又成功跳转页面不同

> 使用 拦截页面跳转的请求

此方案是不通的 还是执行response.json还是会报错

具体代码

```javascript
await page.setRequestInterception(true) 
    let navigationNum = 0
    let requestListener = async request => {
        if (request.isNavigationRequest()){
            navigationNum++
        }
        if (navigationNum >=2 ){ // 第一次为刚进入页面 第二次为跳转到别的页面 进行拦截
            await request.abort()
        }else{
            request.continue()
        }
    }
    page.on('request', requestListener)
```

这种方案是 response.json 也无法获取 页面跳转也没成功

因为跳转url 发起请求的时候 已经跳走了 所以response.json是没法获取的

而我们拦截了这个请求 所以跳转页面会显示一个chrome的错误页面 