# 搭建私有npm

# sinopia


## 安装步骤

1. npm install -g sinopia
2. sinopia
4. npm set registry http://localhost:4873/
5. 访问http://localhost:4873/即可

## npm install逻辑

1. 在本地缓存找
2. 在私有npm找
3. 在公有npmjs

## 更改仓库地址

一般会在安装着家目录中放置配置文件,(在启动sinopia会给出配置地址)

找到config.yml中添加

```javascript
listen:
  - http://120.24.37.111:4873
```
这样修改npmjs路径

## 更改默认配置

例如,目前笔者发现

1. 更改私有npm链接
2. 更改install查找顺序

等常用功能

更多可查: https://github.com/rlidwka/sinopia/blob/master/conf/full.yaml

修改



# 参考链接

1. 对比cnpm和sinopia的安装和使用: http://www.jianshu.com/p/659fb418c9e3
