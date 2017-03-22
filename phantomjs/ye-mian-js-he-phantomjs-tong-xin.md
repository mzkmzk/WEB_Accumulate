# 页面JS和phantomjs通信

# 需求

1. phantomjs->page
2. page->phantomjs


# phantomjs->page

## evaluate

```javascript
page.evaluate(function(atf){
        window.judge_picture_evaluate(atf); 
},_atf)
```

# page->phantomjs

## serve



# 参考链接

1. http://www.barretlee.com/blog/2015/09/25/move-on-phantomjs/: serve通信