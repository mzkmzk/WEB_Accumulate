# 小程序的坑坑洼洼

# 与常规WEB不一样的点

### 判断对象是否存在

```javascript
getCookie && getCookie()
```

一般都这样判断 window.getCookie是否存在

而小程序会直接报错 说getCookie undefined



