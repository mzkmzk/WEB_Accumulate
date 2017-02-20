# beforeunload和unload



# 触发时机

两者触发的时机都是

1. 在浏览器地址栏输入地址，然后点击跳转；
2. 点击页面的链接实现跳转；
3. 关闭或刷新当前页面;
4. 操作当前页面的Location对象,修改当前页面地址;
5. 调用window.navigate实现跳转;
6. 调用window.open或document.open方法在当前页面加载其他页面或重新打开输入流。

# 两者区别

## 顺序

beforeunload在unload之前

## 环境

> beforeunload

1. 页面所有资源均未释放，且页面可视区域效果没有变化
2. UI人机交互失效(window.open,alert,confirm全部失效)
3. 最后时机可以阻止unload过程的执行.(beforeunload事件的Cancelable属性值为Yes)

> unload

1. 页面所有资源(img, iframe等)均未被释放
2. 页面可视区域一片空白
3. UI人机交互失效(window.open,alert,confirm全部失效)
4. 没有任何操作可以阻止unload过程的执行。(unload事件的Cancelable属性值为No)

## 兼容性

![](/assets/xlifecycle-events-testing.png.pagespeed.ic.mLbvU6UX-AQJSwkOff9e.png)

window.addEventListener('beforeunload', function(e){
  var msg = "Do u want to leave?\nChanges u made may be lost."
  //var evt = window.event
  e.returnValue = msg
})

# 参考链接

1. page visbility挺好的讲解: https://www.w3ctech.com/topic/1588