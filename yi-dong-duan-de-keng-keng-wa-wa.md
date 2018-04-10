# 移动端的坑坑洼洼

# Android

# iOS

### iOS11系统中 touchmove preventDefault无效

这是因为addEventListener改了规范 新规范为

`target.addEventListener(type, listener ,{capture: Boolean, passive: Boolean, once: Boolean})`

详细可参考: https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener

这里的passive为true则表明 listener永远不会调用preventDefault()

以用来优化浏览器在处理事件的效率

iOS这个鬼鬼, 从11.3开始 就把这个passive:  设为默认为true辣....

所以导致preventDefault无效

解决这个问题只需显示的改

addEventListener('touchmove', function(){}, { passive: false })