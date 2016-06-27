# 解决跨域问题

首先声明下,这种方法非常不好,但是由于只用在个人的实验性项目中,为了方便采取这种方式,后面会用另外的方式解决

# 1. 问题

在`journey.404mzk.com`中调用`inner.journey.404mzk.com`会出现跨域问题,浏览器会报错

`No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin '' is therefore not allowed access.`

# 2. 解决

