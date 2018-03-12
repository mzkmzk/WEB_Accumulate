# 代理转发

# 实例

# jsonp桩模块转发

例如当测试人员向对一个jsonp接口进行固定的数据返回

但是问题在于 jsnp的callback是不固定的 所以在nginx里配置返回

```javascript
location /query_period_try_info {
            default_type 'application/x-javascript';
            #set_if_empty $arg_callback '_ntes_quote_callback';  

            if ($arg_callback ~* (^[^0-9][0-9a-z._]+$)) {  
                echo_before_body -n "$arg_callback(";  
                echo -n "{ "errno": 10029, 
                          }";  
                echo_after_body -n ");";  
            }  
        }
```

# 参考链接

1. nginx jsonp 桩转发: http://blog.csdn.net/chosen0ne/article/details/7843435