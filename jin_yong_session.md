# 禁用Session

![禁用Session](QQ20161011-0.png)

因为后台没有用cookie来认证用户身份,所以为了减少流量(强迫症),就禁用了Laravel的Session

# 禁用方法

在`app.Http.Kernel.php`

把 `$middlewareGroups`的web数组元素都注释掉

再顺手把$routeMiddleware的元素都注释掉,这样reponse就不会带set-cookie了,心中瞬间清爽了很多