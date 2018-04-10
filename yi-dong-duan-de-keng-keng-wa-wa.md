# 移动端的坑坑洼洼

# Android

# iOS

### iOS11系统中 touchmove preventDefault无效

导致其中一个的影响是 H5监听原因元素touchmove时, 通过preventDefault 阻止浏览器滚动事件

例如不让浏览器滚动等, 失效了, 浏览器还是会滚动

这是因为addEventListener改了规范 新规范为

`target.addEventListener(type, listener ,{capture: Boolean, passive: Boolean, once: Boolean})`

详细可参考: https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener

这里的passive为true则表明 listener永远不会调用preventDefault()

以用来优化浏览器在处理事件的效率

iOS这个鬼鬼, 从11.3开始 就把这个passive:  设为默认为true辣....

所以导致preventDefault无效

解决这个问题只需显示的改

```javascript
document.addEventListener('touchmove', function(event){
    event.preventDefault()
}, { passive: false })
```

那么removeEventListener 是否需要这样呢

```javascript
document.removeEventListener('touchmove', function(event){
    event.preventDefault()
}, { passive: false })

```

答案是不需要的

只有当capture需要跟addEventListener时一致

因为passive和once的值无论如何 都会视为一个监听器
