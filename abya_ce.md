# ab压测

通过php-fpm的慢查询日志.发现系统中一个微信导入功能,因为要把文章中的图片转存到本地.如果图片N多,又没有队列,直接GG

# 1.ab测试

前提

1. 需要登录
2. POST

命令

`ab   -n 10 -c 2 -H "Cookie: laravel_session=59ef9ff8f0fcb776c87b725ea80280cd5726037c"  -T 'application/x-www-form-urlencoded' -t -p wechat_ueditor_post.txt https://test-manager.grouplus.com/ueditor/get_wx_content`

1. -n: 请求次数
2. -c: 并发的请求次数
3. -H: 设置Header
4. -T: POST数据所使用的Content-type头信息。