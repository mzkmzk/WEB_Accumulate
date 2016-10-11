# Webpack

1. react-router
2. 热更新
3. html模板
4. 公共模块抽离
5. css抽离
6. 生产和测试分开
7. redux调试


## 区分开发和生产环境

```javascript
  "scripts": {
    "publish-mac": "export NODE_ENV=prod&&webpack  --progress --colors",
    "publish-win":  "set NODE_ENV=prod&&webpack  --progress --colors"
  },
```

执行`npm run publis-mac`就会执行`publis-mac`


## 把静态资源放在七牛

https://github.com/mzkmzk/qiniu-plugin


## 参考资料

1. AlloyTeam干货: <http://www.alloyteam.com/2016/01/webpack-use-optimization/#prettyPhoto>