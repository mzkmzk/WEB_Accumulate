# react插入html标签字符串

# 问题

```javascript
render(){
  let html_string = "<span>123</span>"
  return (
    <div>
      <p>react好多坑</p>
      {html_string}
    </div>
  )
}
```

react因为防止xss

就会把标签给转义掉,所以页面救护直线显示`<span>123</span>`,因为是转义后的,所以并不会按原来的渲染

# 解决

```html
<div style={{display: 'inline-block'}}
  dangerouslySetInnerHTML=
    {{__html: data_one.status_string}}
></div>

```

这个dangerouslySetInnerHTML的用法,内部不能有元素,不然react会报错

但是这样加div的话 我用div来包括住它,虽然写了下display,但是还是不能保证其在任何情况下都和没div一样

这样是防止了react去转义html字符串

但这只是一种不太好的解决方案

# 思考

其实我把html放在变量里,也是无奈之举

因为这个html会根据别的变量变化

有可能是个a标签

有可能是个span标签

所以就这样做

其实最好的方案还是把这个变量里的元素单独放出来作为一个标签

这样即使在这个单独的component里写几个if else 还是不会太乱



# 参考链接

1. https://facebook.github.io/react/docs/dom-elements.html#dangerouslysetinnerhtml
